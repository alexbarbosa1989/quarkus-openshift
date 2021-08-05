# quarkus-openshift
Basic quarkus native app deployment in Openshift

# OPTION 1: Deploying using s2i method

1. Login into Openshift cluster:
~~~
oc login -u YOUR_USER -p YOUR_PASSWORD https://YOUR_OCP_CLUSTER_ADDRESS:6443
~~~

2. Create a new openshift project (if does not exists):
~~~
 oc new-project quarkus-lab
~~~

3. (Optional) Verify that you are currently located into the created project:
~~~
oc project
Using project "quarkus-lab" on server "https://YOUR_OCP_CLUSTER_ADDRESS:6443".
~~~

4. (Optional) Verify the .s2i/environment file contains:
~~~
ARTIFACT_COPY_ARGS=-p -r lib/ *-runner.jar
~~~

5. Import the OpenJDK11 image that is the base for the quarkus project:
~~~
oc import-image --confirm ubi8/openjdk-11 --from=registry.access.redhat.com/ubi8/openjdk-11
~~~
OPTIONAL: verify the image stream in the quarkus-lab namespace:
~~~
oc get is
NAME         IMAGE REPOSITORY                                                                         TAGS      UPDATED
openjdk-11   default-route-openshift-image-registry.my-openshift-cluster.com/quarkus-lab/openjdk-11   latest    7 seconds ago
~~~

7. Build the quarkus project using the openjdk-11 image as base:
~~~
oc new-app openjdk-11 https://github.com/alexbarbosa1989/quarkus-openshift.git --name=quarkus-test-app
~~~
OPTIONAL: verify that the new image stream is created:
~~~
oc get is
NAME               IMAGE REPOSITORY                                                                                    TAGS      UPDATED
openjdk-11         default-route-openshift-image-registry.apps.my-openshift-cluster.com/quarkus-lab/openjdk-11         latest    5 minutes ago
quarkus-test-app   default-route-openshift-image-registry.apps.my-openshift-cluster.com/quarkus-lab/quarkus-test-app   latest  
~~~

# OPTION 2: Deploying as a Java Application

1. Login into Openshift cluster:
~~~
oc login -u YOUR_USER -p YOUR_PASSWORD https://YOUR_OCP_CLUSTER_ADDRESS:6443
~~~

2. Create a new openshift project (if does not exists):
~~~
 oc new-project quarkus-lab
~~~

3. (Optional) Verify that you are currently located into the created project:
~~~
oc project
Using project "quarkus-lab" on server "https://YOUR_OCP_CLUSTER_ADDRESS:6443".
~~~

4. Clone the project 
~~~
git clone https://github.com/alexbarbosa1989/quarkus-openshift.git
~~~

5. Add execution permissions for `mvnw` file:
~~~
chmod +x mvnw
~~~

6. (Optional) Verify the Openshift properties added in the `application.properties` file, located in src/main/resources/application.properties:
~~~
quarkus.openshift.build-strategy=docker
quarkus.openshift.expose=true
quarkus.kubernetes-client.trust-certs=true
~~~

Those properties enables the deployment process as image stream into Openshift.

7. Deploy the project into the openshift cluster:
~~~
./mvnw clean package -Dquarkus.kubernetes.deploy=true
~~~

8. Verify the deployment in the Openshift namespace:
~~~
oc get pods
~~~
The output should looks like below:
~~~
NAME                     READY     STATUS      RESTARTS   AGE
quarkust-test-1-build    0/1       Completed   0          2m16s
quarkust-test-1-deploy   0/1       Completed   0          25s
quarkust-test-1-g544s    1/1       Running     0          22s
~~~

 

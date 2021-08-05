# quarkus-openshift
Basic quarkus native app deployment in Openshift

# OPTION 1: Deploying as a Java Application

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

5. (Optional) Verify the Openshift properties added in the `application.properties` file, located in src/main/resources/application.properties:
~~~
quarkus.openshift.build-strategy=docker
quarkus.openshift.expose=true
quarkus.kubernetes-client.trust-certs=true
~~~

Those properties enables the deployment process as image stream into Openshift.

6. Deploy the project into the openshift cluster:
~~~
./mvnw clean package -Dquarkus.kubernetes.deploy=true
~~~

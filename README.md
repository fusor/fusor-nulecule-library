Fusor Nulecule Library
======================

Instructions
------------
1. Clone the this repo onto your OSE host
2. docker build miq-memcached-atomicapp
3. docker tag <hash> miq-memcached-atomicapp
4. docker build miq-postgresql-atomicapp
5. docker tag <hash> miq-postgresql-atomicapp
6. copy miq-app-atomicapp/answers.conf.gen to miq-app-atomicapp/answers.conf
7. Edit any values you want to change. The db must be named vmdb_production
8. Ensure you have two 2Gi pv's. oc get pv's to inspect. If necessary: oc create -f extras/pv01i.yaml; oc create -f extras/pv02.yaml
9. For whatever project you choose to use you must give the user privileges to run the miq-app pod
..* Using the miq namespace in the answers file as an example: oadm policy add-scc-to-user privileged system:serviceaccount:miq:default
10. atomicapp run miq-app-appliance
11. oc create -f extras/miq-route.yaml

Example first deployment
-------------------------
oc login  
oc new-project miq  
oadm policy add-scc-to-user privileged system:serviceaccount:miq:default  

oc delete pv pv01; oc delete pv pv02; rm -rf /nfsvolumes/pv0{1,2}; mkdir /nfsvolumes/pv0{1,2}; chmod 777 /nfsvolumes/pv0{1,2}; chown -R nfsnobody:nfsnobody /nfsvolumes/pv0{1,2}; oc create -f extras/pv01.yaml; oc create -f extras/pv02.yaml; oc delete project miq; while oc get pods | grep -q NAME ; do echo "still deleting. sleeping 2 seconds"; sleep 2; done; sleep 10; oc new-project miq; sleep 3; atomicapp run miq-app-atomicapp; oc create -f extras/miq-route.yaml

You can deploy and redeploy as many times as you like by running the last set of commands, as it does all the cleanup to deploy again. Just remember to be in the project miq or the while loop may hang forever.

TODO
----
1. Once a little more certain about the state of the atomicapps, push them somewhere so people don't have to build them or pull them from a git repo.
2. Figure out how to automatically create the routes and pv's.
3. Figure out how to automatically privilege the user.
4. Add parameter bindings so each matching parameter only needs to be filled in once.

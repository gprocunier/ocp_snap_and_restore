#### ocp_snap_and_restore
CRDs facilitating a snap and restore demo


## demo steps

# 01 - Create the project
`oc create namespace file-uploader`
# 02 -build the demo app
`oc new-app  openshift/php:7.2-ubi8~https://github.com/christianh814/openshift-php-upload-demo --name=file-uploader`

`oc logs -f buildconfig/file-uploader`
# 03 - create the route
`oc expose service/file-uploader`
# 04 - create the persistent data volume for the app
`oc create -f ./01-create-data-volume.yaml`
# 05 - modify the deployment to mount the data pv
`oc set volume deploy/file-uploader --add --name=file-uploader-data --claim-name=file-uploader-data --mount-path=/opt/app-root/src/uploaded -n file-uploader`
#
# 06 - connect to the app and upload a file
#
# 07 - create a snapshot of the data pv
`oc create -f ./02-create-snapshot.yaml`
# 08 - wipe out the deployment
`oc delete all -l app=file-uploader`
# 09 - wipe out the data pv
`oc delete pvc -l app=file-uploader`
# 10 - restore the data pv from the snapshot
`oc create -f ./03-restore-data-volume-from-snapshot.yaml`
# 11 - Repeat steps 02, 03
# 12 - Connect to the app and confirm the data is gone
# 13 - repeat step 05
# 14 - wait for the pod to come up and observe that the previously uploaded file has been restored

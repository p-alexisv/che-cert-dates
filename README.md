# che-cert-dates
Extractor of cert expiry dates from credhub-exported data


The script `review-yamls.sh` will process all .yaml files in the current directory.  The .yaml files are expected to be the credhub exports that contain the certificate data.  For each certificate, the script will tell if it's a CA or a certificate, and print the expiry date.


How to use:

1) In the Ops Manager VM, set the environment variables for the bosh commandline credentials.  Get the value of “Bosh Commandline Credentials” from Director tile->Credentials->Bosh Commandline Credentials.

e.g.,
```
$ export BOSH_CLIENT=ops_manager BOSH_CLIENT_SECRET=xxxxxxxx BOSH_CA_CERT=/var/tempest/workspaces/default/root_ca_certificate BOSH_ENVIRONMENT=172.x.x.1
```

2) Run the following to set some more needed env variables for credhub cli to work.

```
$ export CREDHUB_CLIENT=$BOSH_CLIENT CREDHUB_SECRET=$BOSH_CLIENT_SECRET
$ credhub api --server $BOSH_ENVIRONMENT:8844 --ca-cert $BOSH_CA_CERT
$ credhub login
```

3) Run the following to get all the deployment ID’s in the foundation.  For this purpose, we would only want to note down the PKS deployment and the cluster deployments.

```
$ bosh deployments --column=name
```

4) For each of the deployment name from output of step 3, run the following command.  Please change ‘deployment-name’ to the actual deployment name you noted down from step 3.

```
$ credhub export -p /p-bosh/deployment-name > deployment-name_vars.yaml
```

5) Run the shell script to get the cert & CA expiry dates from the credhub output files.  The below command will output to report.txt that you can open to review.

```
$ ./review_yamls.sh > report.txt
```

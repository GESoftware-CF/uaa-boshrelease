# BOSH Release for uaa

## Contents:
* Bosh release files for UAA
* UAA as a git submodule under src - no plans to make changes at this point. Make sure you fetch the submodule before using the release. Use --recursive during pull or git submodule init & git submodule update to get the src/uaa.


## Usage

To use this bosh release:

```
git clone https://github.com/cloudfoundry-community/uaa-boshrelease.git
cd uaa-boshrelease
bosh create release --force
bosh upload release
bosh -n deploy
```
The blobs for postgres, ruby, java, tomcat etc. should get downloaded from S3 community bucket for bosh blobs. 

### Detailed Steps
```
* Start up bosh-lite:  vagrant up --provider=virtualbox
* Set the bosh cli target to bosh lite & login.
  * bosh target 192.168.50.4 lite
  * bosh login
* Create a Bosh release. Everytime you make changes to yaml or configuration files associated with any of the jobs. You need to create a new release. Name it "uaa". Use the flag --force to make sure you create a development release. Final release is created with --final option.
  * bosh create release --force
* This release needs to be uploaded to the bosh director, when running locally it is bosh-lite.
  * bosh upload release
* Upload the stemcell to be used by the bosh director. For bosh-lite we will use: bosh-warden-boshlite-ubuntu-trusty-go_agent.
  * Download this stemcell from bosh.io & then upload it using: bosh upload stemcell <stemcell>
* Create the manifest file. In our case on bosh-lite we need to create manifest for the warden container. Run the following command:
  * templates/make_manifest warden
* Deploy:
  * bosh -n deploy
* Check status: Use the command below to check the VMs & log into the one you want
  * bosh vms
  * Map the IP address of VM running uaa to uaas in your /etc/hosts file. This will allow you to use CURL, browser or uaac to     access to UAA at: http://uaas:8080
  * bosh ssh 
```


### Tips:
 * Check the releases available: bosh releases
 * Check the stemcells available: bosh stemcells
 * Check the VMs and their status: bosh vms


```
templates/make_manifest openstack-nova my-networking.yml
bosh -n deploy
```
### AWS Environment:
  * Use following commands to map port 8080 to 80 on AWS test/staging/prod deployments
  * # iptables -t nat -I OUTPUT -p tcp -d 127.0.0.1 --dport 80 -j REDIRECT --to-ports 8080


### Final releases

To share final releases:

```
bosh create release --final
```

By default the version number will be bumped to the next major number. You can specify alternate versions:


```
bosh create release --final --version 2.1
```

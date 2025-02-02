# Deploying a nginx Server with Blank Page on BOSH Director

This [BOSH](https://bosh.io/) release deploys an nginx webserver.

| Name | Description          |
| ------------- | ----------- |
| stemcell      | ubuntu-trusty|
| nginx-release     | 1.21.6|
| manifest      | nginx_empty.yml|
| Director name      | vbox|


### @. Clone the Repository

```bash
git clone git@github.com:riturajsingh2015/homework.git ~/workspace/homework
```

### 1. SETUP BOSH Director : vbox 
In this part we create a BOSH director named **vbox**

#### 1.1 Deployment Directory
Create a deployment directory for our BOSH director vbox

```bash
mkdir -p ~/deployments/vbox
cd ~/deployments/vbox
```
**Note** - Stay in this directory only 

#### 1.2 Spin BOSH Director

Spin a BOSH director **vbox** using the following command

```bash
~/workspace/homework/bosh/virtualbox/create-env.sh
```

#### 1.3 Login to BOSH Director

- Get Password for BOSH director login and copy it
```
bosh int ./creds.yml --path /admin_password
```
- Try logging in
```bash
bosh -e vbox login
```
- Email : admin
- Password : copied password

### 2. Deploy ngnix server  
In this part we :
- upload a ubuntu-trusty stemcell on BOSH director
- upload a nginx server release on BOSH director
- deploy the **nginx_empty.yml** manifest


#### 2.1 Upload Ubuntu stemcell
Upload the stemcell into the BOSH director.
We used stemcells >= 3541.x; to fix, set the worker's UNIX group problem

```bash
bosh -e vbox us --sha1 2234c87513356e2f038ab993ef508b8724893683 https://bosh.io/d/stemcells/bosh-warden-boshlite-ubuntu-trusty-go_agent?v=3586.100
```

#### 2.2 Upload nginx Release
Upload the nginx release into the BOSH director

```bash
bosh -e vbox ur https://github.com/cloudfoundry-community/nginx-release/releases/download/1.21.6/nginx-release-1.21.6.tgz
```

#### 2.3 Run deployment manifest
- **nginx_empty** manifest uses os: ubuntu-trusty version latest = v3586.100
- network is configred to ::default:: inline with that configured in cloud-config
- static_ips is configured to [ 10.244.0.34 ] to be used for curl output on just one IP 

```bash
bosh -e vbox -d nginx deploy ~/workspace/homework/bosh/virtualbox/manifests/nginx_empty.yml
```

### 3. Test the release
To test the release browse to <http://10.244.0.34/>; you should see a blank page.
or do
```bash
curl 10.244.0.34
```
________________________________________________________________________________


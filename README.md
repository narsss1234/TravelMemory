# Travel Memory

`.env` file to work with the backend:

```
MONGO_URI='ENTER_YOUR_URL'
PORT=3000
```

Data format to be added: 

```json
{
    "tripName": "Incredible India",
    "startDateOfJourney": "19-03-2022",
    "endDateOfJourney": "27-03-2022",
    "nameOfHotels":"Hotel Namaste, Backpackers Club",
    "placesVisited":"Delhi, Kolkata, Chennai, Mumbai",
    "totalCost": 800000,
    "tripType": "leisure",
    "experience": "Lorem Ipsum, Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum,Lorem Ipsum, ",
    "image": "https://t3.ftcdn.net/jpg/03/04/85/26/360_F_304852693_nSOn9KvUgafgvZ6wM0CNaULYUa7xXBkA.jpg",
    "shortDescription":"India is a wonderful country with rich culture and good people.",
    "featured": true
}
```
Solution:

Create an account and deploy a cluster on MongoDB
Once deployed create a login credentials of read and write permission

Netwrok Access was added as 0.0.0.0/0, so that it an be access from anywhere

Use MongoDBCompass to test the login URL

Create two Ec2 instance - preferred Ubuntu - named - Satya_TM_FE, and Satya_TM_BE

Update and Upgrade OS
```
sudo apt update && sudo apt upgrade -y
```

install git on  both the instances

```shell
sudo apt-get install git -y
```

clone the git repo

```
git clone https://github.com/CharismaticOwl/TravelMemory.git
```
Install nodejs and npm on both the instances
```
sudo apt-get update
sudo apt-get install -y ca-certificates curl gnupg
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://deb.nodesource.com/gpgkey/nodesource-repo.gpg.key | sudo gpg --dearmor -o /etc/apt/keyrings/nodesource.gpg
```
version 18.x or later
```
NODE_MAJOR=18
echo "deb [signed-by=/etc/apt/keyrings/nodesource.gpg] https://deb.nodesource.com/node_$NODE_MAJOR.x nodistro main" | sudo tee /etc/apt/sources.list.d/nodesource.list
```
```
sudo apt-get update
sudo apt-get install nodejs -y
```

Steps divided in two parts part A for backend and part B for frontend

**************************PART - A, Deploy backend**************************

cd into backend folder

```
cd TravelMemory/Backend
```
create `.env` file to work with the backend and copy the end point of db and also the port
```
vim .env
```
enter:
```text
MONGO_URI='mongodb+srv://admin:adminadmin1234@cluster0.0b4vnjq.mongodb.net/mern_app'
PORT=3000
```

initialize npm, install to get dependencies and then start the server


```
npm init -y

npm install

node index.js
```
Checked webbrowser, for http://localhost:3000

output should be - Cannot GET /

Which verifies that connection to DB is good to go

Installing Nginx and doing reverse proxy to http://localhost:3000
```
sudo apt install nginx -y

sudo systemctl start nginx

sudo systemctl enable nginx
```
Change the sites-available/default file

```
sudo vim /etc/nginx/sites-available/default
#add below in the file

location / {
    proxy_pass http://localhost:3000;
}
```
restart nginx server

```
sudo systemctl nginx

````

Now test public ip for EC2

http://publi_ip:80

output should be - Cannot GET /

Which verifies that load balancer is set correctly.

**************************PART - B, Deploy frontend**************************

cd into FrontEnd folder

```
cd TravelMemory/Frontend/src
```
change the url.js file with the new private ip where backend is deployed
```
vim url.js

# export const baseUrl = "http://privateIP:80"
```
change directory to frontend
```
cd ..
```

initialize npm, install to get dependencies and then start the server

```
npm init -y

npm install

npm start
```
Checked webbrowser, for http://localhost:3000

Installing Nginx and doing reverse proxy to http://localhost:3000
```
sudo apt install nginx -y

sudo systemctl start nginx

sudo systemctl enable nginx
```
Change the sites-available/default file

```
sudo vim /etc/nginx/sites-available/default
#add below in the file

location / {
    proxy_pass http://localhost:3000;
}
```
restart nginx server

```
sudo systemctl nginx

````

Now test public ip for EC2

http://publi_ip:80

-------------------------------------------------------------
Created multiple instances for both frontend and backend application

Front end instances
```
Satya_TM_FE, Satya_TM_FE_2
i-08e89729efed24ff5, i-09e9494ebbcec8b1e

3.228.13.96, 54.160.23.153 - public IP
10.2.0.98, 10.2.0.84 - private IP
```

Back end instances
```
Satya_TM_BE, Satya_TM_BE_2
i-043feb4666fd6d8bc, i-0c51352d9bee4f414

3.236.137.244, 3.239.41.128 - public ip
10.2.4.38, 10.2.9.59 - private ip
```
Tested and verified that the instances

Modified the code in Frontend index.html page

```
cd TravelMemory/frontend/public

ls

vim index.html
```

Change the title name for Satya_TM_FE as React app 1 and for Satya_TM_FE_2 as React app 2
---------------------------------------------------------
Create Target Group for frontend server

Named it - SatyaTravelMemoryFrontend-TG

ARN - arn:aws:elasticloadbalancing:us-east-1:295397358094:targetgroup/SatyaTravelMemoryFrontend-TG/f5489431b52d1b27

Added the front end instances in the target list

Save
--------------------------------------------------------

Create Load Balancer so that a Load balancer dns is created and then traffic can be handled by AWS.

Named it - SatyaTravelMemoryFrontend-LB
DNS - SatyaTravelMemoryFrontend-LB-547693369.us-east-1.elb.amazonaws.com
ARN - arn:aws:elasticloadbalancing:us-east-1:295397358094:loadbalancer/app/SatyaTravelMemoryFrontend-LB/759e16044e474555

The load balancer is listening at 80, and target group should be set to SatyaTravelMemoryFrontend-TG

Tested and verified that DNS for load balancer is woking and also it is choosing between the app server, as the title name change proves it, React app 1 and React app 2

---------------------------------------------------------
Bought a website from godaddy.com

Name of the website - 
```xyzolo.shop```

--------------------------------------------------------

Configure Amazon Route 53 with the new website

Navigate to Amazon Route53

Create new Hosted Zone

Add domain name 
```
xyzolo.shop
```

Give a description

Keep the option "public hosted zone" checked

Create Hosted Zone

Copy the four nameservers, excluding the "." at the end 

And go to Godaddy, DNS management

Replace the old nameservers with the ones copied from route 53

Save, this will take some time update.

Meanwhile

In route53, create new records for Aname and Cname

Create record

do not add subdomain, make the selection as A record

Select the alias option

Choose the endpoint as alias to Application and classic load balancer
set region
selection the load balancer DNS name

Create Records

Create a new record for C name

set the subdomain
tm.xyzolo.shop

select the record type CAAA - name

Added the value as xyzolo.shop

----------------------------------------

After a few minutes the website is live.

-----------------------------------------
Now configuring cloudflare to help protect from DDos attacks

Create e clous flare account

Added the website name in the Home page

Select the Package, selected free tier here

Cloudflare will automatically import the redirection ips for the loadbalancer

Leave them the way, hit continue

We will see few steps to replace the name server in the DNs management

Copy the new name servers in the godaddy DNS management

This will take some time to load

Once the setup is complete, the instructions that is being shown is removed, and it will show you a confimration message, that the cloudflare is setup correctly.

-------------------------------------------
Tested rigoursly and attached Screenshots to the assignment

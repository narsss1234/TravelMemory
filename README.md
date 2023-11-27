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

PART - A, Deploy backend

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

location /some/path/ {
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

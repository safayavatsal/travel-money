# travel-money

## 1. Backend Configuration
### 1.1 Clone the Repository: 
`git clone https://github.com/UnpredictablePrashant/TravelMemory.git`
`cd TravelMemory`

### 1.2 Set Up Backend: Navigate to the backend directory: 
`cd backend`

Install dependencies: 
`npm install`

### 1.3 Configure Backend: Create or update the `.env` file with the necessary configurations. For example: 

`DATABASE_URL=mongodb://your_mongo_db_url`
`PORT=3000`

### 1.4 Set Up Reverse Proxy with Nginx:

Install nginx:
`sudo apt update`
`sudo apt install nginx`

Configure nginx: Create a configuration file for your backend:

`sudo nano /etc/nginx/sites-available/travelmemory`

Add the following content to the file:

server {
    listen 80;

    server_name your_domain_or_IP;

    location / {
        proxy_pass http://localhost:3000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}

Enable the configuration and restart nginx:

`sudo ln -s /etc/nginx/sites-available/travelmemory /etc/nginx/sites-enabled/`
`sudo nginx -t`
`sudo systemctl restart nginx`

## 2. Frontend and Backend Connection
### 2.1 Update Frontend Configuration: Navigate to the frontend directory:
`cd ../frontend`

Edit urls.js to point to your backend:
`const BACKEND_URL = 'http://your_domain_or_IP:3000';`

### 2.2 Build and Deploy Frontend: Install frontend dependencies and build the React application:
`npm install`
`npm run build`

### 2.3 Serve Frontend: Move the build files to a directory served by nginx. First, create a new directory for serving static files:
`sudo mkdir -p /var/www/html/travelmemory`

Copy the built frontend files to this directory:
`sudo cp -r build/* /var/www/html/travelmemory/`

Configure nginx to serve the frontend:
`sudo nano /etc/nginx/sites-available/travelmemory-frontend`


Add the following configuration:
server {
    listen 80;

    server_name your_frontend_domain_or_IP;

    root /var/www/html/travelmemory;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}

Enable the configuration and restart nginx:
`sudo ln -s /etc/nginx/sites-available/travelmemory-frontend /etc/nginx/sites-enabled/`
`sudo nginx -t`
`sudo systemctl restart nginx`

## 3. Scaling the Application
### 3.1 Create Multiple Instances:
- Launch additional EC2 instances for both the frontend and backend through the AWS Management Console or CLI.

### 3.2 Set Up Load Balancer:
- Go to the EC2 console, navigate to Load Balancers.
- Click Create Load Balancer and select Application Load Balancer.
- Configure the load balancer with HTTP listeners and register your instances.
    - For Frontend instances: Register instances that are serving the React application.
    - For Backend instances: Register instances that are running the Node.js backend.

## 3.3 Update Security Groups:
- Ensure security groups for your instances allow traffic from the load balancer. You might need to adjust inbound rules to allow HTTP traffic from the load balancer’s security group.

## 4. Domain Setup with Cloudflare
### 4.1 Connect Domain to Cloudflare:
- Log in to Cloudflare.
- Add your custom domain and follow the instructions to update nameservers at your domain registrar.

### 4.2 Create DNS Records:
- In Cloudflare, go to the DNS settings for your domain.
- Add a CNAME record:
    Type: CNAME
    Name: www
    Target: Your load balancer's DNS name
- Add an A record:
    Type: A
    Name: @
    Value: IP address of the EC2 instance hosting the frontend

## 5. Documentation and Diagram
### 5.1 Prepare Documentation:
- Document each step in detail, including commands and configurations.
- Include screenshots where relevant (e.g., EC2 instance setup, load balancer configuration, DNS records in Cloudflare).

### 5.2 Create Deployment Architecture Diagram:
- Use `draw.io` to create a diagram.
  - Illustrate EC2 instances (frontend and backend)
  - Show the load balancer
  - Indicate Cloudflare setup and DNS records
  - Demonstrate traffic flow between users, load balancer, and EC2 instances

### Expected Deliverables
- Functional Application:
  - Ensure the TravelMemory application is accessible via your custom domain.
- Documentation:
  - Comprehensive deployment steps with screenshots.
- Architecture Diagram:
  - Clear diagram showing deployment setup.

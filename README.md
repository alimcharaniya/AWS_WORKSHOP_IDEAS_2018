# AWS Workshop - IDEAS LAB 2018 

## Post-Workshop Write Up 

To those who atttended, congratulations! You all successfully deployed a node.JS chat server to an AWS EC2 instance and hosted your own private chat room app somewhere in the cloud using AWS S3. The "cloud" might be a fancy term, but in the end, AWS simplifies the process for getting started.

Lets go over what we did, shall we?

### Key Terms 

- *EC2* - Elastic Compute Containers; servers one can provision and rent from Amazon. 

- *AMI* - Amazon Machine Image; think of this as an operating system. I mentioned that a server is nothing more than a computer that is programmed to serve a single purpose. It doesn't have a GUI. Its job is to handle requests and give responses. That being said, what kind of OS do you want to run on your EC2 instance? That is the question one asks when choosing an AMI. 

- *S3* - Simple Storage Service; object-based storage for any file you can image. A dumbed-down version of dropbox. Snapchat, AirBNB use S3 to store their static files and images as users contribute to their platform. 

- *Key Pair* - In order to access the EC2 server we lease from Amazon, we need a key to get in. AWS allows us to configure key pairs, which use public-key cryptography for encrypting and decrypting login credentials. As an AWS admin, you can create key pairs from within the console for your developement team and remove them as needed. The key-pair is downloaded to your computer in .pem format. We use this key pair to SSH into our server

- *SSH* - Secure Socket Shell; a protocol for logging into remote servers and gaining command line access. 

- *Git* - Industry standard version control software. We used git to transfer code from our local computer to the remote server. Keep in mind not all AWS EC2 machines come with git installed. We had it pre-installed bc we used the Bitnami MEAN AMI. 

- *Web Client* - A client is a program that talks to a server. All apps on your phone are clients of their respective servers. Clients can be mobile apps, developer tools, websites, web apps and more. Our client was an HTML page with some JavaScript that helped us connect our front end to the server via a real time networking protocol called WebSockets.

- *Web Sockets* - WebSocket is a computer communications protocol, providing full-duplex (two way) communication channels over a single TCP connection. 

- *Web Server / Node.JS - Our web server was written in node.js, a JavaScript implementation of a cross-platform runtime language. Pretty much someone was like, why do we only get to write JavaScript in the browser? Let's start using it for servers! Thus, Node.JS was born and it is by far my favorite scripting technology. 

### Concepts you should research/understand if wanting to move forward with AWS 

- *Command Line / Basic Linux Commands* 

- *Git*

- *Basic Understandning of Servers, Routers, Switches, Network Fundamentals* 

- *How does the internet work?* 

- *Code Deployments, Continuous Delivery, Continuous Integration (CI/CD) *

- Load Balancing 

- DNS (Domain Name Service) 

- Databases (MySql, NoSql) 

- ServerLess computing (Cool stuff) 


### Workshop Recap

#### Deploying the chat server to EC2

1. We created accounts at AWS 

2. Familiarized ourselves with the main AWS dashboard 

3. Identified how to switch regions in case we want to deploy servers in a distinct geographic location. 

4. Introduced to 2 fundamental AWS products, EC2 and S3

5. Created our own EC2 instance using a pre-built AMI (Amazon Machine Image) which, in our case, had MEAN-stack-ready dependencies pre-installed. To elaboratet, when you provision a server from AWS, you must install your desired software manually. However, the AMI marketplace allows software vendors to offer server software packages which make it easier for you to get started. In our workshop, we used the Bitnami MEAN AMI. 

6. Created and downloaded a key pair in .pem format. We then attached this key pair to the new instance we created. Remember, only one copy of the key can be made. If you delete it, you lose access to your instance for good. Launched the instance.

7. After a few minutes, the instance booted up. We verified this by getting the public IP address in the AWS console of our instance and pastting it in our browser address bar. You should see a welcome to bitnami webpage. If yes, move forward.

8. We used our local computer's command line to SSH into the remote server. In the same directory where our key pair .pem file resided, we execute the command: 
  - `ssh -i alims_key_pair.pem -p 22 bitnami@<your-public-ip-address>` 
  - https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html
 
9. Boom! We should be logged into our server's command line. Now we need to run our server. Lets copy the code over from github. In your EC2 command line, we ran `git clone https://github.com/alimcharaniya/AWS_WORKSHOP_IDEAS_2018.git`.

10. Now the code is copied. Time to start the server. Ran the following commands:

- `cd AWS_WORKSHOP_IDEAS_2018 && cd server`
- `npm install` (Installs dependencies after referencing package.json
- `node app.js` (Starts the server)

11. You should see the command line is waiting for requests. But how do we make these requests? Node.JS servers run on a specified port. In app.js, you can see our app is running on port 3000. 

12. We need to tell AWS to open up port 3000 to allow incoming requests. Go back to the management console, select your instance. Look in the bottom options panel and find 'security groups'. Click 'view all inbound rules' to modify open ports. Click edit and add port range 3000 as a custom TCP port and allow access from anywhere. Save.

13. Cool! Now the port should be open. Navigate to your instance's public IP in the browser and append `:3000` at the end to trigger a request to our node.js server.

14. You should see an error in the browser and if you are still logged in via SSH to the server, the script should have crashed. This is great because the URL request was correctly routed to port 3000. Go to the SSH session, `control + c` and restart the script with `node app.js` *Our chat server is now running on a remote server*

#### Hosting our chat web page on s3 

1. Lets make sure our chat web page is correctly communicating with our chat server. Open up the /client folder in a code editor of your choice. Look at `main.js`. 

2. On line 3 you will see where our websocket client will try to connect. We need to change the address from localhost:3000 to the address where our chat server is running on AWS. 

3. Copy the public IP of the EC2 instance you created and replace the localhost url in index.html. Don'tt forge to add http:// before the url.

4. Save main.js after modifying the URL and open it in your browser. Try typing a message and click send. If you see the message appear in your chat window. Your app is working correctly! If not, review the last few steps, check if your node server is running in your SSH session. If you're really stuck. Reach out to me.

5. Lets make it so your personal chat room is accessible on the internet. We will use AWS S3 to host this website.

6. From the AWS console, click S3, then create new bucket. In permissions, grant public read access to the bucket.

7. Upload all three files in /client to your S3 bucket

8. Click on bucket properties, then static website hosting and enable it. Type `index.html` as root page and save. In the same window you should see a public URL where your site will be served. Try navigating to that link in your browser and Viola! Your chatroom should be running on the world wide web!


#### Possible Improvements 

Here are some ways to take your chatroom to the next level:

- *Front end* - Make your chat client look pretty! Use HTML/CSS and modify the index.html page to enhance the UI. 
- *AWS* - Purchase your own domain name from AWS Route53 to configure a domain name to point to your server. That way your friends can chat with you privately at www.<yourdomain>.com
- *Backend* - Write some more functionality into the chat server like creating chatrooms, audio, storing messages, etc.
- *CI/CD* - Configure CI/CD pipeline with GitHub incoming webhooks and AWS Code Build / Code Deploy 
- *Serverless* - Run your node.js chat server on serverLess AWS managed infrastructure using AWS Lambda. 
  
  
#### AWS Certificattion and Helpful Info
 - https://aws.amazon.com/certification/
 - https://www.sumologic.com/aws/


##### Anyone reading this is more than welcome to reach out to me for help. alim.charaniya@sjsu.edu

## Repo ontents

### /client

Contains a basic HTML, JavaScript, and CSS web app. The interface uses websockets to connect to our destination chat server. To be hosted as a static websie on s3 or served locally on your computer by opening index.html. 

### /server

Contains Node.JS code we will deploy to Amazon EC2 server. It will act as a standalone microservice.


# SOFTWARE-PROJECTS
by Ifeanyi Omeata

#### Join me as I complete 200+ Software Dev Projects. Follow my Youtube channel at https://www.youtube.com/@omeatai

<details>
  <summary>1 - Mood Based Recipe App</summary>

###

<a href=""><img src="https://github.com/user-attachments/assets/039dee23-0b87-43e9-afeb-9f58f592e7d2" width="720" height="400" /></a>

###

<img src="https://github.com/user-attachments/assets/35150d83-5547-4985-a9d9-b5fa8ddda1c2" width="920" height="520" />

# 1 - Mood Based Recipe App ✅

## **Overview**

- [ ] Create a web application that recommends recipes based on user's mood
- [ ] Use Express.js for the backend server
- [ ] Implement SQLite for recipe storage
- [ ] Build a responsive frontend with Tailwind CSS
- [ ] Allow users to get new recipe suggestions for their selected mood

## **Prerequisites**

- [ ] Install Node.js from nodejs.org
- [ ] Basic understanding of JavaScript, HTML, and CSS
- [ ] A code editor (VS Code recommended)

## **Task 1: Project Setup**

- [ ] Create a new project directory:
```
mkdir mood-recipes
cd mood-recipes
```

- [ ] Initialize a new Node.js project:
```
npm init -y
```

- [ ] [ ] Install required dependencies:
```
npm install express sqlite3 tailwindcss nodemon
```

## **Task 2: Creating a Security Group for the Load Balancer**

- [ ] Navigate to **EC2 Dashboard** → **Security Groups**.
- [ ] Click **Create security group** and configure:
  - **Name:** `LoadBalancer-SG`
  - **Description:** `Security group for the Load balancer`
  - **VPC:** Leave as default
- [ ] Add **Inbound Rules**:
  - **Type:** `HTTP`, **Protocol:** `TCP`, **Port range:** `80`, **Source:** `0.0.0.0/0`
- [ ] Click **Create**.

## **Task 3: Steps to Create the Web Servers**

- [ ] Navigate to **Instances** → **Launch instance**.
- [ ] Configure `webserver-A`:
  - **AMI:** Amazon Linux 2
  - **Instance Type:** `t2.micro`
  - **Key Pair:** Create `myKey`
  - **Network Settings:** Auto-assign public IP: `Enable`
  - **Create Security Group:** `webserver-SG`
    - **HTTP** from `LoadBalancer-SG`
    - **SSH** from Anywhere
- [ ] Add **User Data** for `webserver-A`:
  ```bash
  #!/bin/bash
  sudo su
  yum update -y
  yum install -y httpd
  systemctl start httpd
  systemctl enable httpd
  echo "Response coming from server A" > /var/www/html/index.html
  ```
- [ ] Launch `webserver-A`.
- [ ] Repeat for `webserver-B` with:
  ```bash
  #!/bin/bash
  sudo su
  yum update -y
  yum install -y httpd
  systemctl start httpd
  systemctl enable httpd
  echo "Response coming from server B" > /var/www/html/index.html
  ```
- [ ] Ensure both instances are running in EC2 Dashboard.

## **Task 4: Creating a Load Balancer**

- [ ] Navigate to **Target Groups** → Click **Create target group**.
- [ ] Configure Target Group:
  - **Name:** `web-server-TG`
  - **Health Check Path:** `/index.html`
- [ ] Register both instances and create the target group.
- [ ] Navigate to **Load Balancers** → Click **Create load balancer**.
- [ ] Configure Application Load Balancer:
  - **Name:** `Web-server-LB`
  - **Scheme:** `Internet-facing`
  - **IP** Address Type: `IPv4`
  - **Security Group:** `LoadBalancer-SG`
  - **Listeners:** HTTP, Port `80`, Forward to `web-server-TG`
- [ ] Click **Create load balancer**.

## **Task 5: Create a Bastion Host Server**

- [ ] Navigate to **Instances** → **Launch instance**.
- [ ] Configure `bastion-server`:
  - **AMI:** Amazon Linux 2
  - **Instance Type:** `t2.micro`
  - **Key Pair:** Choose `myKey`
  - **Network Settings:** Auto-assign public IP: `Enable`
  - **Create Security Group:** `bastion-SG`
    - **SSH** from Anywhere
- [ ] Launch `bastion-server`.

## **Task 6: Testing the Load Balancer**

- [ ] Navigate to **Load Balancers** and copy DNS name from **Web-server-LB**.
- [ ] Paste the DNS name in a browser to confirm round-robin response from `server A` and `server B`.
- [ ] Also SSH into the Bastion-server and use curl to send a GET request to the DNS name to confirm round-robin response from `server A` and `server B`.

## **Task 7: Block All other IPs with Security Group: Only Allow specific IP or Group of IPs**

- [ ] Navigate to **Security Group** in **EC2 (Compute)**.
- [ ] Change the InBound rule of the Load Balancer Security Group from All IPs (0.0.0.0/0) to `Public IP of Bastion Host` or `Your IP address`.
- [ ] Navigate to **Load Balancers** and copy again the DNS name from **Web-server-LB**.
- [ ] Paste the DNS name in a browser to confirm round-robin response from `server A` and `server B` is NOT working anymore.
- [ ] SSH into the Bastion-server and use curl to send a GET request to the DNS name to confirm round-robin response from `server A` and `server B` is still working.

## **Task 8: Creating an IP Set**

- [ ] Navigate to **WAF & Shield** → **IP Sets**.
- [ ] Click **Create IP sets** and configure:
  - **Name:** `MyIPset`
  - **Description:** `IP set to block my public IP`
  - **Region:** `US EAST (N. Virginia)`
  - **IP Version:** `IPv4`
  - **IP address:** The Public IP of the Bastion Host or Your `Public IP/32`
- [ ] Click **Create IP set**.

## **Task 9: Creating a Web ACL**

- [ ] Navigate to **WAF dashboard** → **Web ACLs** → Click **Create web ACL**.
- [ ] Configure:
  - **Resource type:** `Regional resources`
  - **Region:** `US EAST (N. Virginia)`
  - **Name:** `MywebACL`
- [ ] Associate **ALB** by Add **AWS resources**.
- [ ] Under **Rules**, **Add rule**:
  - **Rule type:** `IP set`
  - **Name:** `MywebACL-rule`
  - **IP set:** `MyIPset`
  - **Action:** `Block`
- [ ] Click **Create web ACL**.

## **Task 10: Testing the Working of the WAF**

- [ ] Attempt to access ALB DNS name using Curl from Bastion Host.
- [ ] Expect a **403 Forbidden error** showing WAF is functioning.

## **Task 11: Unblocking the IP**

- [ ] Navigate to **WAF & Shield** → **IP Sets** → `MyIPset`.
- [ ] Delete your public IP from the set.
- [ ] Wait, and then retry accessing the ALB DNS name.
- [ ] Confirm access is again available from Bastion Host.

## **Conclusion**

✅ Successfully blocked and managed web traffic using AWS WAF and Security Group! 🎉

</details>



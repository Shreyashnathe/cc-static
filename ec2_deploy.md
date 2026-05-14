## Option D: Deploy Using AWS EC2 (Linux VM)

Use this method if you want to host your static website on a virtual machine (EC2) and control the web server (e.g., Nginx or Apache). This is more advanced and gives you full control, but is not as scalable as S3 for static sites.

### 1. Launch an EC2 Instance

1. Go to the AWS Console and open **EC2**.
2. Click **Launch Instance**.
3. Name your instance (e.g., `static-website-ec2`).
4. Choose an Amazon Machine Image (AMI):

- Select **Ubuntu 22.04 LTS**.

5. Choose an instance type (e.g., `t3.small` for free tier).
6. Create a new key pair or use an existing one (download the `.pem` file and keep it safe).
7. In **Network settings**, allow SSH (port 22) and HTTP (port 80) inbound.
8. Launch the instance.

### 2. Connect to Your EC2 Instance

1. In the EC2 dashboard, select your instance and click **Connect**.
2. Use the SSH command provided, e.g.:

```bash
ssh -i /path/to/your-key.pem ec2-user@<EC2_PUBLIC_IP>
# or for Ubuntu:
ssh -i /path/to/your-key.pem ubuntu@<EC2_PUBLIC_IP>
```

### 3. Install Nginx Web Server

For Ubuntu:

```bash
sudo apt update
sudo apt install nginx -y
sudo systemctl start nginx
sudo systemctl enable nginx
```

### 4. Upload Website Files to EC2

On your local machine, use `scp` to copy files to the EC2 instance:

```bash
scp -i /path/to/your-key.pem index.html styles.css script.js ec2-user@<EC2_PUBLIC_IP>:/tmp/
# or for Ubuntu:
scp -i /path/to/your-key.pem index.html styles.css script.js ubuntu@<EC2_PUBLIC_IP>:/tmp/

```

or clone the repo in VM

```bash
git clone <repo link>
```

Then, on the EC2 instance:

```bash
# Ubuntu Nginx default web root
sudo cp ~/CC-Static_web_Deployement/index.html /var/www/html/
sudo cp ~/CC-Static_web_Deployement/styles.css /var/www/html/
sudo cp ~/CC-Static_web_Deployement/script.js /var/www/html/
sudo systemctl reload nginx
```

### 5. Adjust Permissions

```bash
sudo chown www-data:www-data /var/www/html/*
```

### 6. Test Your Website

1. In your browser, go to: `http://<EC2_PUBLIC_IP>`
2. You should see your static website.

### 7. (Optional) Set Up a Custom Domain and HTTPS

1. Point your domain's A record to the EC2 public IP.
2. For HTTPS, install Certbot and follow instructions to get a free SSL certificate from Let's Encrypt.

### 8. Updating the Website

Repeat the `scp` and `mv` steps above whenever you want to update your site files.

---

**Summary:**

- EC2 gives you full control but requires more setup and maintenance.
- For most static sites, S3 is easier and cheaper, but EC2 is a good option if you need to run custom server logic or want to learn about Linux web hosting.

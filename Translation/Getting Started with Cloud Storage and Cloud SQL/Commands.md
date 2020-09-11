## Translation of lab Getting Started with Compute Engine to 100% command line.

## These commands lines have been executed using the Cloud Shell.

- Create an instance named **bloghost** with image disk **Debian GNU/Linux 9 (stretch)** localised in the zone **us-central1-a**, with the **HTTP traffic** allowed and a **startup script**.

`gcloud compute instances create bloghost --image debian-9-stretch-v20200910 --image-project debian-cloud --zone us-central1-a --tags http-server --metadata startup-script="apt-get update apt-get install apache2 php php-mysql -y service apache2 restart"`

- Enter your chosen location into an environment variable called **LOCATION**.

`export LOCATION=US`

- In Cloud Shell, the **DEVSHELL_PROJECT_ID** environment variable contains your project ID. Make a bucket named after your project ID:

`gsutil mb -l $LOCATION gs://$DEVSHELL_PROJECT_ID`

- Retrieve a banner image from a publicly accessible Cloud Storage location.

`gsutil cp gs://cloud-training/gcpfci/my-excellent-blog.png my-excellent-blog.png`

- Copy the banner image to your newly created Cloud Storage bucket.

`gsutil cp my-excellent-blog.png gs://$DEVSHELL_PROJECT_ID/my-excellent-blog.png`

- Modify the Access Control List of the object you just created so that it is readable by everyone:

`gsutil acl ch -u allUsers:R gs://$DEVSHELL_PROJECT_ID/my-excellent-blog.png`

- List machines type.

`gcloud sql tiers list`

- Create a Mysql instance named **blog-db** and set the region to **us-central1**.

`gcloud sql instances create blog-db --tier=db-n1-highmem-16 --region=us-central1`

- Set the **root** password.

`gcloud sql users set-password root --host=% --instance blog-db --password Sql@1234`

- List the Sql instance and copy the **ipAdress**

`gcloud sql instances describe blog-db`

- Add a new user for the instance named **blogdbuser**

`gcloud sql users create blogdbuser --host=% --instance=blog-db --password=SQL@1234`

- Enale **Ip** to be assign to the instance

`gcloud sql instances patch blog-db --assign-ip`

- Add **authorized** network

`gcloud sql instances patch blog-db --authorized-networks=VM_BLOGHOST_IP/32`

- Open a command prompt on the **bloghost** instance.

Set the zone to avoid suprises

`gcloud compute ssh bloghost --zone=us-central1-a`

- In th ssh session on bloghost, change your working directory to the document root of the web server:

`cd /var/www/html`

- Use the nano text editor to edit a file called index.php:

`sudo nano index.php`

- Paste the content below into the file:

```
<html>
<head><title>Welcome to my excellent blog</title></head>
<body>
<h1>Welcome to my excellent blog</h1>
<?php
 $dbserver = "CLOUDSQLIP";
$dbuser = "blogdbuser";
$dbpassword = "DBPASSWORD";
// In a production blog, we would not store the MySQL
// password in the document root. Instead, we would store it in a
// configuration file elsewhere on the web server VM instance.

$conn = new mysqli($dbserver, $dbuser, $dbpassword);

if (mysqli_connect_error()) {
        echo ("Database connection failed: " . mysqli_connect_error());
} else {
        echo ("Database connection succeeded.");
}
?>
</body></html>
```
- Restart the web server:

`sudo service apache2 restart`

When you load the page, you will see that its content includes an error message beginning with the words:

```
Database connection failed: ...
```

- Return to your ssh session on bloghost. Use the nano text editor to edit index.php again.

`sudo nano index.php`

```
<html>
<head><title>Welcome to my excellent blog</title></head>
<body>
<h1>Welcome to my excellent blog</h1>
<?php
 $dbserver = "YOU_BLOG-DB_IP";
$dbuser = "blogdbuser";
$dbpassword = "YOUR_blogdbuser_PASSWORD_HERE";
// In a production blog, we would not store the MySQL
// password in the document root. Instead, we would store it in a
// configuration file elsewhere on the web server VM instance.

$conn = new mysqli($dbserver, $dbuser, $dbpassword);

if (mysqli_connect_error()) {
        echo ("Database connection failed: " . mysqli_connect_error());
} else {
        echo ("Database connection succeeded.");
}
?>
</body></html>
```

- Restart the web server

`sudo service apache2 restart`

- Edit **index.php** to add an image from **the bucket named after the project**

`sudo service apache2 restart`

`sudo nano index.php`

- Paste this image tag.

`<img src='https://storage.googleapis.com/PATH_TO_THE_FILE/my-excellent-blog.png'>`

- Restart the web server

`sudo service apache2 restart`

Check the result in the browser.
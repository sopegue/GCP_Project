## Translation of lab Getting Started with Compute Engine to 100% command line.

## These commands lines have been executed using the SDK Cloud.

- Create an instance named **my-vm-1** with image disk **Debian GNU/Linux 9 (stretch)** localised in the zone **us-central1-a**  and with the **HTTP traffic** allowed.

`gcloud compute instances create my-vm-1 --image debian-9-stretch-v20200910 --image-project debian-cloud --tags http-server --zone=us-central1-a`

- Set the default zone to **us-central1-b**.

`gcloud config set compute/zone us-central1-b`

- Create a second instance named **my-vm-2** with the following command.

`gcloud compute instances create "my-vm-2" --machine-type "n1-standard-1" --image-project "debian-cloud" --image "debian-9-stretch-v20190213" --subnet "default"`

- **List** the instances created.

`gcloud compute instances list `

- Open a command prompt on the **my-vm-2** instance.

`gcloud compute ssh my-vm-2`

- Use the ping command to confirm that my-vm-2 can **reach my-vm-1** over the network:.

`ping my-vm-1`

- **Exit** and **Open** a command prompt on the **my-vm-1** instance.

Be sure to set the instance compute zone since **the current default zone** is **us-central1-b** while the **my-vm-1** instance zone is set to **us-central1-a**.

`exit`

`gcloud compute ssh my-vm-1 --zone=us-central1-a`

- At the command prompt on **my-vm-1**, install the Nginx web server:

`sudo apt-get install nginx-light -y`

- Use the nano text editor to add a custom message to the home page of the web server:

`sudo nano /var/www/html/index.nginx-debian.html`

Use the arrow keys to move the cursor to the line just below the h1 head and add the text **Hi from YOUR_NAME**

Press Ctrl+O and then press Enter to save your edited file, and then press Ctrl+X to exit the nano text editor.

- Confirm that the web server is serving your new page. At the command prompt on **my-vm-1**

`curl http://localhost/`

- **Exit** and **Confirm** that **my-vm-2** can **reach the web** server on **my-vm-1**

`exit`

`gcloud compute ssh my-vm-2`

`curl http://my-vm-1/`

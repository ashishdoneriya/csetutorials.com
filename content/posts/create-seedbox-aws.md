---
title: How to create a seedbox using AWS
date: 2020-03-14T07:14:31+05:30
lastmod: 2020-03-14T00:04:00+05:30
description: How to create a seedbox using aws and download torrent files.
slug: create-seedbox-aws
categories:
  - Internet
tags:
  - AWS
  - seedbox
---

In this article I'm going to tell you how to create a seedbox using [AWS (Amazon Web Service)](/what-is-amazon-web-services.html). Using seedbox we can download torrent files like Ubuntu disk image etc.

To create a seedbox first I will create a aws lambda instance (virtual machine) and then activate seedbox using [cloud-torrent](https://github.com/jpillora/cloud-torrent). So here are the steps.

**1.** Login to you aws account. If you don't have an aws account then create it and then login. You have to have a credit or debit card for creating an account in aws.

**2. Open Lightsail service.**

![AWS Lightsail](/wp-content/uploads/2019/12/aws-lighsail.png)

**3. Create an instance.**

![Aws Lightsail Create Instance button](/wp-content/uploads/2019/12/aws-lightsail-create-instance-button.png)

**4. Under `Select a blueprint` label. Click on `OS Only`.**

![AWS Lightsail Select OS Only](/wp-content/uploads/2019/12/aws-lightsail-select-os.png)

**5. Select Ubuntu 18.04 OS.**

![AWS Lightsail select Ubuntu 18.04 OS](/wp-content/uploads/2019/12/aws-lightsail-select-os-ubuntu.png)

**6. Select instance type**  
![Select instance type in Lightsail](/wp-content/uploads/2019/12/aws-lightsail-instance-type.png)

I would recommend you to select a minimum $5 plan. In $5 plan you have to pla $5 a month for the machine in which you get 1 GB Ram, cpu of 1 core, 40 GB SSD Storage and 1 TB of total data transfer bandwidth.

**7. Now click on create instance button at the end of the page.**  
![AWS Create Instance](/wp-content/uploads/2019/12/aws-lightsail-create-instance-button-final.png)

Your instance (machine) will be up and running in a few minutes.

**8. Now click on the terminal icon.**  
![](/wp-content/uploads/2019/12/aws-lightsail-terminal-icon.png)

This will open a terminal in a new window.

![](/wp-content/uploads/2019/12/aws-lightsail-terminal.png)

**9. Run the below command in the terminal**

```bash
curl https://i.jpillora.com/cloud-torrent! | sudo bash
```

![](/wp-content/uploads/2019/12/aws-lightbox-terminal-cloud-torrent-installation.png)  
This will install cloud-torrent app using which we'll start seedbox.

**10. Execute below command and press enter button 4-5 times and after that close the window (by clicking on the X button in left or right or whatever)**

```bash
nohup sudo cloud-torrent --port 80 &
```

![](/wp-content/uploads/2019/12/lightsail-turn-on-seedbox.png)

**11. In previous window click on the title Ubuntu-1.**

![](/wp-content/uploads/2019/12/aws-lambda-1.png)

This will openup the details of this machine. Copy this public ip and open it in your browser.

![](/wp-content/uploads/2019/12/aws-lightsail-public-ip.png)

**Your seedbox is ready.**

![](/wp-content/uploads/2019/12/aws-seedbox-screenshot.png)

To delete the instance, first stop the machine, and then delete.

![](/wp-content/uploads/2019/12/aws-lightsail-delete-machine.png)

Here is the video of all steps
{{<raw>}}
<iframe src="https://www.youtube.com/embed/ZhtmSEyPyKg" width="560" height="315" frameborder="0" allowfullscreen="allowfullscreen"></iframe>
{{</raw>}}
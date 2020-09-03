+++
title = "h. Using the DCV Web Client"
weight = 100
tags = ["tutorial", "cloud9", "ParallelCluster"]
+++
#### Cutting and Pasting

If you use Chrome, you are asked if you would like to allow cutting and pasting, and then cut and paste will just work. 

However, if you are using a different browser, the normal cut and
paste shortcuts will not work in the DCV web client. These are
directions for you.

To paste from your local machine to the DCV desktop
- Select the local text and copy it.
- Choose "Paste to Remote Session" from the icon menu in the upper left of the window (see below).
![Cutnpaste](/images/neuropoint/cutnpaste.png)

- A window opens; paste your contents into it. You will see a check mark in the box.
- Go to the application in the DCV desktop that you wish to paste into, and use normal paste commands.

To paste from the DCV web client back to your local machine
- Select the text and copy it.
- Choose "Copy to Local Device" from the icon menu in the upper left of the window.
- A window will open fleetingly, and close. 
- Go to your local desktop and use normal paste commands.


#### Connection timeouts
If your connection times out, you can reconnect by generating another link.

```bash
pcluster dcv connect -k ~/.ssh/neuropoint-key npointclust
```

Select this entire link and paste it into a browser tab.








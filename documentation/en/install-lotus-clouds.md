# Cloud Images Instructions

Filecoin has a set of cloud images at each of the major clouds, such as AWS, GCP and Azure. It is an Ubuntu-based images with Lotus node and [Powergate](https://github.com/textileio/powergate) installed. It is automatically renewed with a CD mechanism on a daily basis. One can get these images to start using Filecoin test network almost immediately. All you need to do is to catch up the daily difference with the network.

Installation is cloud-specific

## AWS

To get AWS image simply go to your [aws console](https://console.aws.amazon.com/) at [AMI's page](https://console.aws.amazon.com/ec2/v2/home?region=us-east-1#Images:visibility=public-images;search=filecoin;sort=name).
First choose the region (US East 1 (N. Virginia), Asia Pacific (Singapore) or
EU Central 1 (Frankfurt)), after that select `Public AMI` at the dropdown menu and search for Filecoin images. At the search bar you can add 711012187398 as the image owner to ensure that Filecoin images are official and shorten the output. All the official images are named as `filecoin-<tag>-<creation_date>`, where `<tag>` is one of the tags from [our GitHub](https://github.com/filecoin-project/lotus/tags) and the `<creation_date>` is the accurate date and time when the images were created to reflect the timestamp of Lotus chain. Note that some of the old tags might be missing as there is no need to keep the old releases.

Select appropriate image and press `Launch` button.

**Warning!** Beware of fakes. Make sure AMI is owned by 711012187398 account before launch.
    
 More detailed steps one can find [here](https://aws.amazon.com/premiumsupport/knowledge-center/launch-instance-custom-ami).

## GCP

To create Filecoin VM on GCP you need to create a custom image in your project from archive stored on our Google Storage acocunt and launch VM from this custom image.

To create a custom image go to the `Compute engine` => `Images` and click `Create image`. Fulfil all the necessary fields. Please look at this [article](https://cloud.google.com/compute/docs/images/create-delete-deprecate-private-images#bundle_image).
For example:
1) Set ‘Filecoin’ as the image name
2) Select `Cloud Storage file` as a source for an image and specify the following link as the cloud storage file link => *filecoin/filecoin-<tag>-latest.tar.gz*. Get <tag> from [our GitHub](https://github.com/filecoin-project/lotus/tags).
3) Click "Create"

Once the image is created - you can launch VM from this custom image. To launch a VM go to `Compute Engine` => `VM Instances` and click `Create`. Change the boot disk to the custom image created at the first step. Adjust Machine type to fulfil the node technical requirements. Press `Create` to complete the process of the VM creation.

## Azure

To create a VM on Azure you'll have to complete following steps:

1) Log into your Azure account and create a storage account or use existing one to be able to upload VHD in the next step. Copy Filecoin image into your storage account using the Azure CLI.

```
 az storage blob copy start --source-uri $URL \
 --connection-string $CONNECTION_STRING \
 --destination-container nameyourcontainer \
 --destination-blob filecoin.vhd
```

where `$CONNECTION_STRING` is connection string for your storage account and `$URL` is *https://filecoin.blob.core.windows.net/container/filecoin-<tag>-latest.vhd*

2) Once the VHD is copied - go to [MS Azure Image creation tab](https://portal.azure.com/#create/Microsoft.Image-ARM) and specify uploaded VHD as the `Image Source`.

3) The last step is to deploy [Filecoin VM](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/quick-create-portal) using custom image that you have created at the previous step.

# Operations Instructions

Both Lotus node and Powergate are installed as a services inside of an image and launches automatically. It can can be managed via SystemD.

Use `sudo systemctl status/start/stop/restart/enable/disable` command with `lotus-node.service` or `powergate.service` arguments to manipulate the Lotus node or Powergate service accordingly. 

When installing the node make sure to secure the RPC port (`1234/TCP`). Node is installed according to the Lotus bestpractices, however it is worth remembering that end user is responsible for securing it's own node.
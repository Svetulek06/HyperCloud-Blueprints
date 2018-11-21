
How to Guide to be able to call Google Cloud Asset Inventory API.
 
1.	Login to GCP console and make sure you select the right project that you are working on. Create API endpoint on GCP Console: 
    a.	Go to APIs & Services tab, click on Enable API
    b.	Then search for Cloud Asset API and then Enable it.
 
2.	Go to IAM & admin over at GCP Console and create a Service Account. Give it a name, id and description.
  
3.	Edit the newly created service account and then create a Key with JSON Format. Save the .json file for now, in your machine.
4.	Go to IAM tab, and look for the newly created service account and add these 2 roles into the service account: 
    a.	Project  Viewer
    b.	Cloud Asset  Viewer
 
5.	Create a storage bucket over at GCP Console Storage tab. Give it a unique name, the location, availability and geo-redudancy option don’t really matter (as long as it is not Coldline)
 
6.	Upload the json key file generated and create a link to make the json file downloadable from URL by adding “allUsers” user Reader on the json file object. Then get the link.
 
 Note the link and now the GCP account is ready to perform Cloud Asset API, we move to HCP.
 
7.	Over at HCP, Import the plugin that is in the zip file. Go over App Store  Plugins, Click on New and Select Shell.
Select Action on the top right and click on import, and import the file. Fill in the appropriate data and Click Save.
  
8.	Generate an GCP instance (this has an assumption that you already have GCP Provider working…if you don’t then you will need to setup GCP provider. It is not covered in this guide). 
a.	Note: Pick Ubuntu 16.04 Instance, size don’t matter because it is not really executing anything, and inbound roles not really required (every call is outbound). It needs internet.
 
9.	Execute the plugin by clicking on the instance and Plugin tab and pass the appropriate parameters.
  
10.	When it is done, the output file will be at the bucket target specified.
 

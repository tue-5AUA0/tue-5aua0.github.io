# Google Colab
Google Colab is the perfect way to prototype and test your networks. It provides a free use of their GPU instances using
IPython Notebooks to write and execute Python code in your browser. It also allows GitHub and Google Drive integration.

## Set-up and installing custom libraries
First, to use a GPU or other accelerator, navigate to `Runtime > Change runtime type`. Here, select the desired 
`Hardware accelerator`.

Command line access is also provided in Colab, indicated by prepending your line with an exclamation mark:
```
!pip install package_name
```

## GitHub integration
To access public notebooks, replace the GitHub domain (https://github.com) with 
https://colab.research.google.com/github/.

It is also possible to access private notebooks. For this, you must first link your GitHub account. For this, go to 
https://colab.research.google.com/github/ and check the `Include Private Repos` checkbox. In the popup window, sign-in 
to your Github account and authorize Colab to read the private files. The private repositories and notebooks are now 
available via the GitHub navigation pane.

## Working with data
### Google Drive
If you want to mount your Google Drive, paste the following code in a block and follow the instructions after running 
it (go to the specified URL and copy the authorization code).
```
from google.colab import drive
drive.mount('/content/drive')
```

### Google Cloud Storage Buckets
First, specify your project ID:
```
project_id = 'Your_project_ID_here'
bucket_name = 'Your_bucket_name_here'
```

And in order to access GCS, you must be authenticate:
```
from google.colab import auth
auth.authenticate_user()
```

Finally, to download a file from the bucket, use the following command:
```
!gsutil cp gs://{bucket_name}/to_upload.txt /tmp/gsutil_download.txt
```
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

It is also possible to access private repositories. This section is converted to markdown from the notebook located 
[here](assets/Colab_GitHub_SSH_access.ipynb), and provides some boilerplate code for accessing your private GitHub 
repos using SSH access. If you want to run this code in Colab, just download the Jupyter notebook from the link and 
upload it to Colab when creating a [new notebook](https://colab.research.google.com/notebooks/intro.ipynb#recent=true).

Only the first codeblock contains values you should fill in yourself, and to allow future access
you want to copy the output of the private key to the second to last codeblock. Also, you will need to manually add your
public key to the GitHub repository's settings page (explained in the relevant section). This code is created (and 
slightly adjusted) from the tutorial found 
[here](https://medium.com/@ashkanpakzad/data-into-google-colab-5ddeb4f4e8).

### General Set-Up


```
from google.colab import drive
drive.mount("/content/gdrive")

USER_NAME='INPUT_YOUR_GITHUB_USERNAME_HERE'      # e.g. 'johndoe'
USER_EMAIL='INPUT_YOUR_GITHUB_EMAIL_HERE'        # e.g. 'john.doe@example.com'
REPO_NAME='INPUT_YOUR_PRIVATE_REPO_NAME_HERE'    # e.g. 'my_project', without '.git'
```

### Generate an SSH-key pair to connect to GitHub
If you first run this notebook, you need to generate an SSH-key pair to connect to GitHub. If you are prompted any 
questions, hit enter. If you have already done this, skip to the next section.


```
!ssh-keygen -t rsa -b 4096 -C $USER_EMAIL
```

Now, copy the content of the generated keys. The first line displays the public part of the SSH key pair. This one 
should be copied to your GitHub repository. So, switch over to your Github, open up your chosen private repository and 
open the repo’s *settings>deploy keys*. Click *'Add deploy key'*, paste the code and give it a name, and finally hit 
*'add key'*.

The second line (formatted as a block) is the private key. If you want future access to your repository using Google 
Colab (i.e. after your instance is automatically deleted), you will need to copy this key to replace 'private_key' in 
the next section.


```
!cat /root/.ssh/id_rsa.pub
!cat /root/.ssh/id_rsa
```

Finally, you are able to clone into your repository. First, this needs to add the public key of GitHub to your known 
hosts (otherwise GitHub cannot be found). Then, it is possible to clone into your repo using the specified username and
repository name, creating a folder of that same name to clone into.


```
!ssh-keyscan github.com >> /root/.ssh/known_hosts
!chmod 644 /root/.ssh/known_hosts
!chmod 600 /root/.ssh/id_rsa
!ssh -T git@github.com
!git clone 'git@github.com:'$USER_NAME'/'$REPO_NAME'.git' '/content/'$REPO_NAME
```

### If you already have your key pair set up
For access to your private GitHub repo (after setting up your SSH keys), you will have to write your private key to 
*/root/.ssh/*

The private key you have generated in the steps before should have been copied in the cell below for future use. Now, it
is as simple as running this code block (which writes your generated private key to the correct folder, and clones into 
your repo again.


```
private_key = """
-----BEGIN RSA PRIVATE KEY-----
PASTE_IN_YOUR_PRIVATE_KEY_HERE_(REPLACE_ONLY_THIS_LINE_KEEPING_THE_BEGIN_AND_END_RSA_PRIVATE_KEY_LINES)
-----END RSA PRIVATE KEY-----
"""

!mkdir -p /root/.ssh
with open('/root/.ssh/id_rsa', 'w', encoding='utf8') as fh:
  fh.write(private_key)
!ssh-keyscan github.com >> /root/.ssh/known_hosts
!chmod 644 /root/.ssh/known_hosts
!chmod 600 /root/.ssh/id_rsa
!ssh -T git@github.com
!git clone 'git@github.com:'$USER_NAME'/'$REPO_NAME'.git' '/content/'$REPO_NAME
```

### \[Your own code here\]
Now, you have full access to GitHub, so you can try things out like '!git status' to see what your setup is. It is 
probably required to first make the repository folder active, so run '%cd' first (see first code block).


```
%cd $REPO_NAME
```


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
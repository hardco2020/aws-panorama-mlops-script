# aws-panorama-mlops-script

## Note

This repo is manly a helper for aws-panorama-mlops to install APP + Model successfully, here are some tips about how to change model + app

## How to replace your model + app in  PPE

### Replace Model

If you want to change your model for your end customer, you will need to **have a training image in ECR of any random account** 

Go to https://github.com/hardco2020/aws-panorama-mlops-script, and clone the repo 

<img width="1788" alt="Screen Shot 2022-08-24 at 2 53 21 PM" src="https://user-images.githubusercontent.com/61721490/186350822-e9911fb6-6d32-43b2-b5b5-4bd5e14d98a8.png">

open the **yolov5_pull_and_push.sh**, change the line 14 , **set the image to be your ECR image name**

<img width="1151" alt="Screen Shot 2022-08-24 at 2 53 48 PM" src="https://user-images.githubusercontent.com/61721490/186350903-ab0a6707-18a6-4578-a29f-1d4b94c9a009.png">

```
image = <your_image_name>
```

change the line 28, **set the src_id to be your account_id,** and if region is different, change the region as well, after that, save the file and push to the repo you fork.

```
src_id= <your_account_id>
src_region= <your_region>
```

go to the **aws-panorama-mlops** repo you forked earlier when deploying PPE

**(Optional: Open a new branch since you will be changing some of the codes in PPE )**

go to **amplify/backend/custom/customResource/customResource-cloudformation-template.yaml, you will see the following code** 


[Image: Image.jpg]
At the line289, you will see we clone the **ppa_model_script** repo, change the repo to be your account 

![Screen Shot 2022-08-24 at 2 59 16 PM](https://user-images.githubusercontent.com/61721490/186351900-037e4b60-4cb2-44ef-87de-d70865e54334.png)

```
sudo git clone
https://github.com/<your_account_name> / aws-panorama-mlops-script.git
```

go to **amplify/backend/function/createTrainingJob/src/index.py, then go to line 50**

you will see the following code.

![Screen Shot 2022-08-24 at 3 00 24 PM](https://user-images.githubusercontent.com/61721490/186352109-28f6044d-5269-467e-8dde-b9b6cee36973.png)


**Change the line 50 <gary-yolov5-train> to be your image name** 


```
training_image = (
    account_id
    + ".dkr.ecr.ap-southeast-1.amazonaws.com/<your_image_name>:latest"
)
```


After that, please remember to **push your code to Github, if you just need to change the model, you can go on and deploy your APP with your updated amplify-oneclick**

NOTE: **You will need to delete your PPE or deploy a whole new PPE in new account  before you deploying again** with new model ( **if you just do a normal update it wouldn’t work!!!** ) 


### Replace APP

Go to https://github.com/hardco2020/aws-panorama-mlops-script

Fork the repo if you haven't done it in the last step when replacing the model

Upload your new **Panorama APP** to the repo, please make sure it’s at the same level with **deploy/ppe_panorama_app** 

Go to **deployment / buildspec.yaml**

<img width="1176" alt="Screen Shot 2022-08-24 at 3 03 59 PM" src="https://user-images.githubusercontent.com/61721490/186352840-5ad61bd5-d66e-4b78-8333-bfc2bbabcbcf.png">

Change the line 15, **set the ppe_panorama_app to be the folder of your new app**

```
cd <your_app_folder_name>
```

After that remember to **push the code to the repo,  and go to amplify-oneClick ( if you didn’t change your model, you can directly go to Replace App without redeploying**

go to **amplify/backend/custom/customResource/customResource-cloudformation-template.yaml**

you will see the following code.

![Screen Shot 2022-08-24 at 3 04 37 PM](https://user-images.githubusercontent.com/61721490/186352944-8aeac086-7608-4d60-af27-35ea15e98860.png)

Change the line 476 to be your account name 

```
Location: https://github.com/<your_github_account>/aws-panorama-mlops-script.git
```

Then **push the code to Github, after that you can go on and deploy your APP with your updated amplify-oneclick**

NOTE: **You will need to delete your PPE or deploy a whole new PPE in new account  before you deploying again** with new model ( **if you just do a normal update it wouldn’t work!!!** ) 


## Replace APP without re-deploying

if you just need to change your application, after you upload your new App to your forked account, you can just change your  CodeBuild destination at [AWS CodeBuild Console](https://ap-southeast-1.console.aws.amazon.com/codesuite/codebuild/projects?region=ap-southeast-1&projects-meta=eyJmIjp7InRleHQiOiIiLCJzaGFyZWQiOmZhbHNlfSwicyI6eyJwcm9wZXJ0eSI6IkxBU1RfTU9ESUZJRURfVElNRSIsImRpcmVjdGlvbiI6LTF9LCJuIjoyMCwiaSI6MH0)


<img width="799" alt="Screen Shot 2022-08-24 at 3 06 10 PM" src="https://user-images.githubusercontent.com/61721490/186353231-0ad78e2a-c494-49b8-9a0f-7854ed450d35.png">

Choose the **Build-Panorama-App,** click on the **Edit** and choose the **Source**

<img width="799" alt="Screen Shot 2022-08-24 at 3 06 23 PM" src="https://user-images.githubusercontent.com/61721490/186353286-3af34482-1e8e-4378-8c53-310f3182c04a.png">

**Connect to your Github, and change the Repository URL to be your new repo** 

<img width="799" alt="Screen Shot 2022-08-24 at 3 06 23 PM" src="https://user-images.githubusercontent.com/61721490/186353374-d368c36e-9fdc-4f74-b678-941a75a1cf25.png">

After that just click **Update source**, and you can start building a new app in **Package Application.**

**Note: If you deploy a new version of PPE, you will have to redo this step, since the CodeBuild will still be point to our Github account if you didn’t change it in amplify-oneclick**

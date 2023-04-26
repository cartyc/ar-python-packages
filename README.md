# Python Packages in Artifact Registry Example

This repo is a simple example of using Artifact Registry to store Python Packages. The official docs can be found[here](https://cloud.google.com/artifact-registry/docs/python)

## Environment

Set the project environment variable
```
export PROJECT_ID=your-project-id
```

Config gcloud to use this by default
```
gcloud config set project $PROJECT_ID
```

## Install Twine

This will be used to push the python packages to AR

```
pip install twine
```

## Create an Artifact Registry

Create the AR instance to store the Python Pacakages

```
gcloud artifacts repositories create python \
    --repository-format=python \
    --location=northamerica-northeast1 \
    --description="Python packages for in Artifact Registry"
```

Set the AR in your gcloud configs
```
gcloud config set artifacts/repository python
```

## Authentication

Install keyring to store the credentials for PIP twine to authenticate with against the AR instance.

```
pip install keyring
pip install keyrings.google-artifactregistry-auth
gcloud auth application-default login
```

## Packages!

Create a dir to store the packages in
```
mkdir pip-packages
mkdir pip-packages/dist
cd pip-packages
```

cat the packages list into a `requirements.txt` file so we can tell pip to download them.
```
cat <<EOF > requirements.txt
flask 
requests 
waitress 
google-auth 
google-cloud-storage 
marshmallow 
google-cloud-asset 
protobuf 
google-cloud-logging 
google-cloud-securitycenter
EOF
```

### Download the packages

```
pip download -r requirements.txt -d dist
```

### Upload Packages to AR
```
python3 -m twine upload --repository-url https://northamerica-northeast1-python.pkg.dev/${PROJECT_ID}/python/ dist/*
```

## Config Setting for Pip to be able to download Packages

Follow the instructions printed by this command to update your `.pypirc` and `pip.conf` files.

```
gcloud artifacts print-settings python --project=${PROJECT_ID} \
    --repository=python \
    --location=northamerica-northeast1
```

You can now use `pip` to install the packages

```
pip install -r requirements.txt
```
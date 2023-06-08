# GoS3-matic



> "Bridging external tools with GoCD and S3 made easy!"



GoS3-matic is an easy-to-use tool for integrating external tools with GoCD via AWS S3. It is helps to  annotate S3 artifacts with metadata in order to trick GoCD into treating them as native S3 materials.  This enables the use of the [gocd-s3-artifacts Plugin](https://github.com/indix/gocd-s3-artifacts) plugin to trigger pipelines and retrieve artifacts.



## Description


GoCD is a fantastic open-source and free continuous delivery server. Unfortunately, integrating third-party tools and artifacts into the GoCD ecosystem can be difficult. The standard way to trigger an	automated pipeline run is either through SCM,  another pipeline, or a package repository.
SCM is great for starting the initial build of deliverables; the other pipelines are a simple method for building a network of pipelines within GoCD.  
However, GO.CD is not that great at doing initial builds,  as its strengths are in being a  delivery server, not a build or integration server.  External package repositories can help with this.  
GoCD has very nice plugins for   YUM and Apt Linux repositories and various Java binary repositories such as Artifactory.  
These are fantastic, but the world does not revolve solely around Linux and Java applications.  
The S3Material plugin is used in this case.  You can use this plugin to create an AWS S3 bucket to store any type of artifact.   However, there is a minor flaw in how the S3Artifact plugin works.  
When you upload a file to S3, the S3Material plugin does not activate a pipeline.  
It doesn't do it  because it needs to ensure atomicity for complex multi-file deliverables.  
If the S3Material plugin starts a pipeline whenever something new appears in S3, it may begin before all files associated with the deliverable have been uploaded.
To avoid this, the metadata technique is used.  
Once the upstream pipeline  finishes uploading all the files, it has to put an annotation on the S3 folder with metadata in the specific format. The S3Materials plugin detects a change in metadata, and notifies GoCD that a new package has been added to the repository, which  results in the activation of one or more pipelines that rely on this package.  
The metadata technique also allows GoCD to communicate important information to GoCD, such as the identity of the user who created the package, the upstream version number, and links to the upstream job that triggered the build.  
The metadata technique is fantastic, but populating the metadata of S3 objects is not as simple.  This is where GoS3Matic comes in handy.



GoS3-matic solves this issue by providing a simple and efficient way to annotate S3 packages with the necessary metadata. With GoS3matic, you can easily mark S3 artifacts with pipeline labels, usernames, traceback URLs, and custom keys, making them appear to have originated from another GoCD server.





## Usage



Run GoS3-matic using the following command:



```shell
python annotate_s3.py annotate <bucket_name> <path> --label <pipeline_label> [--username <username>] [--traceback-url <traceback_url>] [ --quiet ]
```

Replace the placeholders with your desired values:

- `<bucket_name>`: Name of the S3 bucket
- `<path>`: S3 object prefix
- `<pipeline_label>`: Pipeline label (integer)
- `<username_or_email>`: Username or email (optional)
- `<traceback_url>`: Traceback URL (optional)


Additional options:

- `--quiet` or `-q`: Suppress the display of the parameter table

For example, to annotate S3 packages with GoS3-matic, use the following command:

```shell
python annotate_s3.py annotate my-bucket artifacts/ --label 42 --username john@example.com --traceback-url https://example.com/traceback --key my-annotation
```


## AWS Credentials

GoS3-matic requires AWS credentials to interact with the S3 service. The tool supports the following methods for providing AWS credentials:

1. **Environment Variables**: Set the following environment variables with your AWS access key ID and secret access key:

   ```shell
   export AWS_ACCESS_KEY_ID=<your-access-key-id>
   export AWS_SECRET_ACCESS_KEY=<your-secret-access-key>
   ```

   Replace `<your-access-key-id>` and `<your-secret-access-key>` with your own AWS credentials.

2. **Configured Profile**: If you have configured profiles using the AWS CLI, GoS3-matic can use a specific profile. Set the `AWS_PROFILE` environment variable to specify the desired profile:

   ```shell
   export AWS_PROFILE=<your-profile-name>
   ```

   Replace `<your-profile-name>` with the name of the AWS profile you want to use.

3. **Instance Profile (within AWS)**: If running GoS3-matic within an EC2 instance or another AWS resource that has an associated IAM instance profile, the tool can automatically use the credentials provided by the instance profile. No additional configuration is required.

Make sure to configure your AWS credentials appropriately before running GoS3-matic to ensure proper authentication and access to the S3 service.


## License

This project is licensed under the [MIT License](LICENSE).

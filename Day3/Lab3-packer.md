# Lab 3: Template Creation using Packer

Duration: 30 minutes

### Creating a Packer Image

To build an image packer utilizes a JSON file with the following sections...

##### [Builders](https://www.packer.io/docs/builders/index.html) (required)
* responsible for creating machines and generating images from them for various platforms.
* You can have multiple builder types in one file.

Below is an example of a basic builder for AWS EBS AMI.
Create a new json file called `web-vistors.json` with the following builder.

```json
{
  "variables": {
    "aws_source_ami": "ami-039a49e70ea773ffc"
  },
  "builders": [
    {
      "type": "amazon-ebs",
      "region": "us-east-1",
      "source_ami": "{{user `aws_source_ami`}}",
      "instance_type": "t1.micro",
      "ssh_username": "ubuntu",
      "ssh_pty": "true",
      "ami_name": "tmp-{{timestamp}}",
      "tags": {
        "Created-by": "Packer",
        "OS_Version": "Ubuntu",
        "Release": "Latest"
      }
    }
  ],
  "provisioners": [
    {
      "type": "shell",
      "inline": [
        "mkdir ~/src",
        "cd ~/src",
        "git clone https://github.com/hashicorp/demo-terraform-101.git",
        "cp -R ~/src/demo-terraform-101/assets /tmp",
        "sudo sh /tmp/assets/setup-web.sh"
      ]
    }
  ]
}

```



Validate your configuration.

```shell
> packer validate web-vistors.json
```

##### Running Packer
Once the file is ready we will need to dothe following steps...

1. **packer validate web-vistors.json** - If properly formatted the file will successfully validate
    * This command will work just fine if all the variables are within the main packer file, but if you want to pass user variables from a different file the command will have an additional flag **packer validate web-vistors.json**


   
##### Resources
* Packer [Docs](https://www.packer.io/docs/index.html)
* Packer [CLI](https://www.packer.io/docs/commands/index.html)
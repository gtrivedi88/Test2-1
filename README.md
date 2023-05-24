Enabling hermetic builds
Hermetic builds are isolated from the outside world. This means that the build is not dependent on any external resource, such as network access or access to a file system. This makes hermetic builds more secure and reliable.

To support hermetic builds, {ProductName} uses the following build tools:
Python
Golang

Once you have chosen a build tool, you need to configure the build to be hermetic. This involves specifying the necessary dependencies and creating a virtual environment for the build. 

{ProductName} uses Cachi2, a CLI tool that pre-fetches your project's dependencies to aid in making your build process hermetic.

Enabling Hermetic Builds in Python
Prerequisites
A repository that contains the application’s source code.
You have used the Custom build pipelines feature when creating an application or used the Manage build pipelines feature after you created an application. This ensures the existence of the `.tekton` folder exists in your application’s source code.

Procedures

Go to your application’s source code.
Open the `.tekton` directory and make the following changes to both the `.yaml` files, pull, and push.
Set the hermetic pipeline parameter to `true` in your pipeline definition.
- default: "true" 
  description: Execute the build with network isolation
  name: hermetic
  type: string

Set the prefetch-input pipeline parameter to `pip`.
- description: Build dependencies to be prefetched by Cachi2
  name: prefetch-input
  type: string
  default: "pip"

Setting up Cachi2 to prefetch dependencies.
Add a project file, for example, pyproject.toml, with some metadata to the root of your source code. This is necessary because Cachi2 actively searches for the name and version of your project.

[metadata]
name = "my_package"
version = "0.1.0"


Note: You can also add a setup.py or setup.cfg project file instead of a  pyproject.toml project file. For information on the metadata of these files, see Project Metadata.

For Cachi2 to work properly, the requirements.txt file must list all the project’s dependencies, including any transitive dependencies. Additionally, each dependency in the requirements.txt file must be pinned to a specific version. 

If
Then
You’re using the sample repo
Replace the requirements.txt file with http://pastebin.test.redhat.com/1094080
You’re using your own repo
To generate a fully resolved requirements.txt file run, pip-compile --generate-hashes pyproject.toml

Note: The command may change based on the project file name you used in step 3.a.


Additionally, since we’re building all the wheels from the source, in some cases, it is also necessary to declare build dependencies. To allow building from source in a network-isolated environment, see requirement-build.txt.



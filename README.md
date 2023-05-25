I had this defintion on hermetic builds: Hermetic builds refer to a build process that is isolated from external dependencies, such as network access or a file system. This makes hermetic builds more secure and reliable.

SME feedback: I view hermetic builds just as being sealed off from unfettered access. This means that all required resources and dependencies MUST go through the build system. It allows the build system to ensure that it has the ability to collect, query, analyze, etc. all dependencies needed for the build. Hermetic itself does not necessarily have a direct connection to security/reliability. It might have implicit connections to it.

I think the wording should be changed a little. I was just trying to figure out what the definition sources were. There are many different ways that I have sen it be defined. As an example, builds are not being isolated from external dependencies as those are still being provided by Cachi2.

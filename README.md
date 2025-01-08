# static-tools
Statically compiled Linux tools

My motivation to create these builds was for Unraid usage, as Nerd Tools is deprecated/unmaintained,
and sometimes I need tools that are not included in the distribution.

Each tool has it's own (orphaned) branch, and includes a Jenkinsfile to describe the build process + whatever else is needed.
I'm using an Alpine image in those as my base (given musl is king for static binaries).

Built binaries can be found on my Jenkins instance here: https://build.lochnair.net/job/static-tools/

## Build list

| Name  | Version |  Link |
| ------------- | ------------- | ------------- |
| rclone  | 1.68.2  | TBA |
| screen  | 5.0.0  | [Download](https://build.lochnair.net/job/static-tools/job/screen/lastSuccessfulBuild/artifact/screen) |

# Day 1/40 - Docker Tutorial For Beginners 🐳 - Docker Fundamentals - CKA Full Course 2024 ☸️


## Check out the video below for Day1 👇

[![Day1/40 - Docker Tutorial For Beginners](https://img.youtube.com/vi/ul96dslvVwY/sddefault.jpg)](https://youtu.be/ul96dslvVwY)



## What is Docker

![image](https://github.com/piyushsachdeva/CKA-2024/assets/40286378/2f8eb0eb-8c2d-4460-8dbc-c43e1f3fce3e)


## Understanding Containers V/S Virtual Machines

![image](https://github.com/piyushsachdeva/CKA-2024/assets/40286378/b1bfe6ae-a1e6-4b04-8486-272d3ed380bc)
- Container are light weigth alternative of VMs
- Hyperviser allow to run multiple VM on a single physical host.
- Container engine allow to run multiple containers on a Virtual or physical host.

## Containers V/S Virtual machines with the help of a Building and House analogy


![image](https://github.com/piyushsachdeva/CKA-2024/assets/40286378/48061343-195d-4299-8815-0856e9b5af71)
- Container have a shared infra(H/W resources and OS), on Physical server or VM can run a multiple instance of same application.
- Vm also used share resource but not OS, only run a single instance of application.
- Binaries and dependencies can be used by different application inn VM but in container the binaries and dependencies can only used by the application in container.
- Container user infra more efficiently and use optimally

## Challenges with the non-containerized applications(traditionally)

![image](https://github.com/piyushsachdeva/CKA-2024/assets/40286378/58b4c2dd-6abe-4acd-9318-c718e4133a91)
- Application and there code is seperate and also deployed and configured separately, due to this there can be environment inconsistency.
- So application can work on some environment or can't work on some environment due to this inconsistency.
  
## How Docker solves the challenges

![image](https://github.com/piyushsachdeva/CKA-2024/assets/40286378/a8f134d8-b70e-4c99-857e-5da26e68674b)


## That's how docker was born(Just kidding!)

![image](https://github.com/piyushsachdeva/CKA-2024/assets/40286378/c781a038-3420-4980-a3d8-ab123fc33d95)



## A Simple Docker WorkFlow

![image](https://github.com/piyushsachdeva/CKA-2024/assets/40286378/444db8f4-1cbb-47b0-986f-489292f05b7c)
- Developer write a dockerfile in organizations, devops engineer help to write a docker file.
- Dovops engineer need to know how to write a docker file.
- Starting point in docker workflow
- Registry is like VCS for docker image like git&github for source code.
- Dockerhub is the registry for public images which is desined to store the docker images.
- Docker endorse hub.docker.io

## Docker Architecture

![image](https://github.com/piyushsachdeva/CKA-2024/assets/40286378/79099c53-7f63-4bb6-885c-28cdd0850d93)

# Introduction to DevOps
In DevOps, it is not just about learning technologies and tools, it's also about integrating them together. 

For a long time, IT industry is divided into two zones: `development` (developers, testers, build and release) and `operations` (Delivering the software and its features to the end users, like system administrators, cloud engineers). Over the period of time, because of Agile and other methodologies, `development has become very fast, but the operation is still slow`. So the software and its feature are not getting delivered as fast as it's getting developed. `DevOps solves this problem of delivery`. Whenever you have code and end user, you need DevOps. 

## DevOps
DevOps can accomplish tasks that used to take hours in minutes, so the companies can focus more on their business or the product. 

Software development lifecycle (SDLC): 
1. Requirements gathering and analysis info collection (product features, users, usage info, user requirements, market state, etc). 
2. Planning (Cost, resources, risk). 
3. Design. Architects will design it based on the inputs from prv phase, and produce design documents
4. Development based on design documents. 
5. Testing. Identify the defects to ensure the product quality. 
6. Deploy and maintain. Deployed to prod env so users start using the product. Operations team and System admins make sure that the software is up and running all the time. Maintenance: changes and uptime. 

Models in SDLC:
- Waterfall. Each phase must be completed before the next phase can begin. Cannot accommodate changing requirements. Working software is produced very late in the lifecycle. 
- Agile. Divide all requirements into smaller lists, and work on a list for 2 to 4 weeks and then move on to the next list and so on and so forth. Demonstration of the features can be given to client after every iteration and based on their feedback, new ideas can be injected into the next iterations. 
- Spiral
- Big bang
- ...

Development with agile is about regular and quick changes, while Operations are IT Infra Library (ITIL) driven, provides stable environment for the product, and complains about no clear instructions from the development side. The problem is that Dev is agile, but Ops is still waterfall. This is where DevOps comes in. A DevOps consultant would tell everyone to work collaboratively, communicate effectively, and integrate the entire code delivery process. He explains and train dev team on ops concepts so they can communicate with ops team more effectively. He also train Ops team on Agile concepts so ops can collaborate with dev team. And automation training (code build, code testing, software testing, infra changes, deployments) and instruction to every team across the board. So the dev and ops start working together - each task is automated in the delivery process by every team, so the whole process can be integrated together. 

DevOps Lifecycle: 
Code (developers commits code) -> Code Build (Deployable software: Artifact) -> Code Test (Unit & Integration test) -> Code Analysis (Vulnerability, best practices) -> Delivery (Deploy changes to staging) -> DB/Security changes (Every other ops changes) -> Software Testing (QA/Functional, load, performance, tests) -> Deploy to Prod -> Go Live (User traffic diverted to new changes) -> User Approval (User Feedback) -> Keep Monitoring (DevOps Lifecycle)

## Continuous Integration (CI)
Continuous integration is an automated process in DevOps, which generates software and its features quickly and efficiently. 

Traditionally, developers write code and work as a team. They store all this code at a version control system like Github. They pull an push code a few times a day (code changes happens continuously). This code will be moved to the build server to be built and tested, which generates the software (artifact at this stage), which is stored in a software repo. This artifact will be packaged in a specific format (war or jar in Java; DLL/EXE/MSI in Windows, etc). This artifact is then shipped to servers for further testing. Then after software testers approve, it can be shipped to production servers. 

Now assume these developers have produced 3 weeks of code. Next, this code will be fetched by the Build server, and this code is built and tested. If there are a lot of errors, now developers have to fix these defects and update the code at a lot, lot of places. This could have been much easier if the problem was detected very early in the process. The fact is that the code is getting merged into the repository, but not really getting integrated.

The solution: after every single commit from the developers, the code should be built and tested, so no waiting and collecting all these codes with bugs. 

But developer commits several times in a day, so it's not humanly possible to do a build & release several times in a day. So we need to automate this process (such as using Jenkins) - when the developer commits any code,  an automated process will fetch the code, build  and test it, and send a notification if there is any failure. As soon as the developers receives a failed notification, they will fix the code and commit it again. So on an so forth, build and test new changes, if it's good, then it can be versioned and stored in a software repository.

In a cyclic view: code -> fetch -> build -> test -> notify -> feedback -> (to the beginning, which is code)

This automated process is called continuous integration (CI). The goal of CI is to detect defects at a very early stage, so it does not multiply.

Tools involved: 
IDE is used by developers for coding (such as Eclipse, Visual Studio, Atom, PyCharm, ...). 
These IDE will be integrated with version control system to store and version the code (such as Git, SVN, TFS, Perforce, ...). 
Build tools based on the programming language (Maven, Ant, Gradle, MSBuild, Visual Build, IBM Urban Code, Make, Grunt, ...). 
Software repositories to store artifacts (such as Sonatype Nexus, JFrog Artifactory, Archiva, CloudSmith Package, Grunt, ...). 
Continuous integration tools that integrate everything (Such as Jenkins, CircleCI, TeamCity, Bamboo CI, Cruise Control).

## Continuous Delivery (CD)





















 
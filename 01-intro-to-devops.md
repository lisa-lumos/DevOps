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

## Continuous Integration
























 

Identifying process using linux commands

ps --forest -o pid,%mem,%cpu,tty,stat,time,cmd -g $(ps -o sid= -p 11015)

Remarks	Search string 	Java Service!	Service name	Servie name
Ä	Ä	Ä	Ä	Ä	Ä
Ä	Ä	Ä	Ä	Ä	Ä
ISPTomcatBootstrap	ps -aef --forest|grep infa | grep ISPTomcat	Java Service	JAVA	Node Service
InfaTomcatBootstrap	ps -aef --forest|grep infa | grep _AdminConsole	Java Service	 	Admin Console
 jsf-service-bootstrap.jar	 	Java Service		Model Repo Service
InfaTomcatBootstrap	ps -aef --forest|grep infa | grep SearchService	Java Service		Search Service
 	ps --aef --forest |grep infa | grep pmrepagent	 	 	Repository service
InfaTomcatBootstrap	ps -aef --forest |grep infa | grep Scheduler	Java Service	 	Scheduler_Service
InfaTomcatBootstrap	ps -aef --forest|grep infa | grep Content	java Service		Content mgmt service
 	ps --aef --forest |grep infa | grep pmserver	 	 	Integration Service
InfaTomcatBootstrap	ps -aef --forest|grep infa | grep Analyst	java Service		Analyst Service
jsf-service-bootstrap.jar	ps -aef --forest|grep infa | grep Resource	java Service	 	Resource Manager Service	 
jsf-service-bootstrap.jar	ps -aef --forest|grep infa | grep WebService	java Service		WebServices Hub Service
InfaTomcatBootstrap	ps -aef --forest | grep EmailService	Java Service		Email Service
 	ps --aef --forest |grep infa | grep pmserver	 	 	Integration Service
InfaTomcatBootstrap	ps -aef --forest|grep infa | grep DataIntegration	Java Service		Data integration Service	 

3 main services - Compute, Storage, Networking
Cloud Attributes
	Predictability
		Predictability is knowing that your application will perform at a consistent level. This is achieved through a combination of autoscaling, high availability, and load balancing. Though not noted in the question, it also describes transparency in costs.
		Predictability is knowing what your application will cost with real-time tracking of resource usage. It also allows forecasting future costs based on current usage. Though not noted in the question, predictability _also_ describes knowing that your application will perform consistently regardless of customer load.
	Reliability
		Reliability refers more to the resiliency of an application even in the case of partial or wide-scale failures or outages.
	Scalability
		Scalability describes the ability to add resources to meet demand.
	Manageability
		Manageability has two aspects: 1. How you create and manage resources, which includes autoscaling, template-based deployments, and monitoring/alerts. 2. How you interact with your cloud environments, including via a web portal, command line, and programmatic APIs.
	Governance
		Governance describes enforcing standards via templates and policies that dictate what can and cannot be created. It also includes the ability to audit cloud environments to alert if any resources are out of compliance.
Azure CLI and Azure PowerShell are cross-platform command-line tools that can be installed on macOS, linux and windows
Cloud Shell is launched from the Azure portal. Inside Cloud Shell, you have access to both Bash and PowerShell environments.
 Azure compute
	iaas
		Under the shared responsibility model, management of the resource is shared between the cloud provider and the end user. The cloud provider being responsible for the cloud services infrastructure and the end user being responsible for the service being configured and managed correctly.
		The client is responsible for all guest VM OS and application updates.
		Services can be scaled automatically to support system load.
		When you deploy a VM in Azure, the licensing for the OS is typically included in the hourly charge.
		VM
			only the hardware is maintained by the az (networking can be)
		Availability set
		  An availability set consists of 2 or more virtual machines in the same physical location within an Azure datacenter. This configuration ensures that only a subset of the virtual machines in an availability set will be affected in the event of hardware failure, OS update, or a fault domain issue, since the VMs would reside on different racks. Availability zones protect applications from complete Azure datacenter failures, which affect all VMs within the Availability set, however datacenter failures were not a requirement in this scenario.
		Scalesets
			provides identical load balanced vms, which can be activated or deactivated based on the usage
			each scaleset intially starts with a baseline vm, which is cloned acc 
			scalesets ensure high availability, consistency of appl because of the autoscaling feature
		Vertical scaling
			Vertical scaling requires powering off a virtual machine to change the amount of CPU/RAM on a machine, which is not performed automatically.
			This is also known as scaling up, where the compute capacity of an existing resource is changed, such as adding more CPUs/RAM to an existing VM.
	paas
		app services
			they are called as Fully managed platform, means the storage, networking and servers and other basic infra is controlled by az. it provides the ability to scale automatically and add features to these custom applications
			there are 3 types of apps ser
				web apps
					these are the direct web appl hosted on azure
					there are multiple tiers of hosting
						free and shared - free
						basic - 10gb 3 instances
						standard - 50gb 10 instances
						premium - 250gb 30 instances
						isolated - 1tb 100 instance
				web apps with container
					these are the containers which can be mounted anywhere and are managed by the az kuberntes ser
				api apps
					these are the direct apis deployed on the az with custom based networking and storage protocols
		az container instances
			its a az service that it used to run the container workloads( i.e appl or processes)
			we can create the container img when its only required, saving the cost
			Containers fulfill the requirement to bundle an application with its dependencies and ensure portability. Azure Container Instances allows you to only run your container when needed and have lower management overhead compared to a fully orchestrated Azure Kubernetes Service option.
		Azure Kubernetes Service (AKS)
			Kubernetes uses containers, which fulfills the requirement to bundle an application with its dependencies and ensure portability. Compared to Azure Container Instances, which are more suitable for running single containers on demand, AKS is more suitable for large-scale orchestration of multiple containers interacting with each other.

Azure Networking
	virtual network - vnet
		the vnet is the network through which the az resources such as vm and internet and also the on prem networks, etc communicate with each other
		the vnet is virtual, coz the hardware i.e routers are virtual managed by az
		each vnet is assigned with a specific address space - the list of addresses that can be assigned to different resources
		the vnet can also have multiple subnets created, this is used to
			group certain type of resources into a sub group
			there is a much more efficient handling of addresses to smaller subnets
			we can assign specific nsg to subnets for more security
		we can scale the vnets easily (add more address)
		we can maintain high availabilty by peering 2 vnets together
		we can isolate all the resources efficiently using nsg and subnets
		be default, all the resources connected to a particular vnet must be present at one region only, but we can also enable multi region vnet peering
		vnet peering
			this is a type of connection established btw 2 vnet for comm
			the conn is present on the microsoft private netweork, not exposed to public
			this peering is common to enable multiple vnets to talk with low latency and multi region access
	load balancer
		az provides load balancer similar to nginx lb for managing traffic to the muliple vm instances deployed on a vnet
	cdn
		az has cdn too similar to akamai
	vpn gateway
		virtual network gateway - vnet gateway
			the vnet gateway comprises of two or more vms deployed to a particular subnetof vnet also known as gateway subnet
			the vms are created in th subnet when you create a vnet gateway
		vpn gateway is a specific type of vnet gateway that is use to connect the vnet with the on prem network, which sends the data over a vpn connection obviously over the internet
		vpn gateway is necessary for hybrid arch
		Azure VPN Gateway allows you to establish an IPsec tunnel from an on-premises network to an Azure virtual network over the public internet.
	application gateway - layer 7 lb
		appl gateway is a high level load balancer i.e lb basically routes the traffic based on availability and the ip addr, where as the appl g routes traffic based on the http request method or url, etc
		it supports high scalabilty and availability
		it is generally used in zone redundancy, where one appl g is used for routing traffic across multiple zone, rather than setting up lb for each zone
	express route
		express route is better option than vpn gateway connecting the on prem network to az vnet, which has very low latency, where it uses dedicated az datacenter for routing the traffic
		it doesnt use the public internet to transfer the data
	Components required for create a secure IPsec tunnel over the public internet from an Azure virtual network to an on-premises location.
		Gateway subnet
			This scenario calls for a VPN connection over a virtual network gateway. A gateway requires its own gateway subnet on an Azure virtual network. 
		Virtual network gateway
			This scenario calls for a VPN connection over a virtual network gateway. A gateway component is one of the components needed to create this connection.
		VPN device
			This scenario calls for a VPN connection over a virtual network gateway. Site-to-Site connections to an on-premises network require a VPN device. 

Storage
	Choosing the right type of storage is very critical for the appl
	Storage Account in which we store the data is a Unqiue Azure namespace, where every object stored has a unique web url i.e. <storagename><storage-type>.core.windows.net/<container-name>/<file-name>
	these storage accounts are iaas solutions
	each storage acc has 2pb of max storage can store unlimited number of files
	they have a deafult tier - hot/cold
	Blob (Binary Large Object)
		Each storage account can have multiple Blob Contrainer, which hold multiple blob objects
		Each of the blob object can be of any type and size, which can be accesed by the unique url
		blob type sto can be used for long term storage appl with archive type too
		Examples of blobs = Images, Videos, Log files, Data storage
		There are 3types of blob
			Block type
				Made up of individual blocks of data i.e binary objects
			Append Type
				Block blocks which are best suited for append operations i.e Growing log file
			Page type
				Any part of the file can be accessed at any time (Similar to Harddisk)
		Pricing tiers for blobs
			Hot - Low acess time and high cost (freq accessed files)
			cool - low cost and high acc time
				The Cool storage tier is ideal for infrequently access files at a lower cost; however, the files are still readily available unlike the Archive tier.
			archive -very low cost and highest acc time
				The Archive storage tier does meet the requirement to store files at a lower cost; however, Archive tier files are not readily accessible as there is a delay between requesting the files and when they are available. Based on this requirement, the Cool tier is the better option.
				The Archive tier also has a "rehydration" time before the files are accessible;
	Disk (Managed disk storage)
		Managed disk is the physical disk we generally attach to the vms
		Az takes care of uptime and backup of these disk storage
		Disk storage is a full Virtual hard disk that you can access. It is ideal as the disk for a Virtual machine. In fact, when you create a Virtual machine, disk storage is created too.
		4 types of disk sto
			HDD
			Standard SSD
			Premium SSD
			Ultra Disk (Used for txn hvy loads)
				Ultra disks are the most expensive, yet highest-performing disk types available for Azure virtual machines. They support up to 64TB on a single disk.
	File Storage
		File Sto is the shared file sys drives spread across multiple teams or indi
		Az file sto is managed(Hardware), bkp, resilient
		Generally, cmpies have hybrid or cmplte on cloud file sto sol
	Archive Data Sto
		Archive data is the data which is not freq accessed, kept for legal purp
		Az has archive data sto which is based on blob sto
		its enc, stable and is of lowestcost 
	Azure Files
		Azure Files uses SMB Server Message Block shares within a storage account to act as a cloud-based network file server.
		Azure Files are hosted inside of a storage account.
Data Redundancy
	Zones within a region are separate locations or datacenters in the same geographical area. Deploying additional machines to another zone will increase the reliability of the solution in case one of the datacenters is unavailable.
	availability zones provide a level of fault tolerance within a single region (or general location). Each availability zone is a self-contained datacenter.
	data transfer within same region is free and outbound transfer is proced
	Az always stores multiple copies(min of 3) of data to avoid data redundancy
	the copy process is invisible, takn care by az
	there are 4 diff types of data redun options
		Locally redundant sto - lrs (Single Region)
			Stores 3 copies within primary region and within a single avail zone
			these 3 copies are stored on 3 diff servers on diff server racks
			tolerant if one server rack fails
		Zone redun sto (Single region)
			Stores 3 copies of data within pri reg but within 3 diff az
			tolerant even if one az fails
		Geo redun sto (multi region)
			Stores 6 copies of data - 3 copies within primary reg and within single az and other 3 in sec reg and within 1 az
			availabilty is high, tolerant if region fails
		Read only Geo redun sto
			RA-GRS allows you to have higher read availability for your storage account by providing “read only” access to the data replicated to the secondary location. Once you enable this feature, the secondary location may be used to achieve higher availability in the event the data is not available in the primary region. This is an “opt-in” feature which requires the storage account be geo-replicated.
		geo zone redun sto (multi region)
			Stores 6 copies of data - 3 copies within primary reg and within 3 diff az and other 3 copies in sec reg and within 1 az (imp)
			availabilty is highest, tolerant if region fails
Data migration
	For occasional data transfers from on prem to az, there are 3 ways
		Azcopy
			cmd uti to copy the blobs and file sto types 
		Storage explorer
			gui for cpying of multiple data types
		Az file sync
			syncs the file sto sol of on prem to cloud
			users can access the file sto which is a hybrid sol of on prem and cloud sto
	For Large data transfers, there are 2 ways
		Az Data Box
			Used in the case of Large data trans but has a limited bandwidth
			uses a offline method to trans the data from on prem to cloud or vicev which uses a rugged enc hard drive(data box)
			some use case scenarios
				Initial bulk data movement
				Disasater recovery at on prem loc
				Security req (multiple copies of sensitive data is to stored)
		Az Migrate
			Its a roadmap to migrate everything from on prem to cloud
			it can include trnasfer of vms, web apps, storage, databases, servers, etc
			based on the requirements, the migration process can happen
			Azure Migrate is a full cloud migration and modernization platform. One of its features is the ability to discover dependencies of resources being migrated to Azure.
Premium Sto options
	the standard sto option is the (general purpose v2 acc) whihc uses the hdd sto option with multiple redun options
	General-purpose v2 is the standard performance option which supports all storage types. It provides an acceptable level of performance for most workloads; however, does not include high performance low-latency operations. It also costs less than the premium performance options.
	but if we want faster acc times, we can choose the premium sto types which is based on the sto type
	when we want premium sto types, we tradeoff the redun options (all options are single region redun based only)
	there ar 3 types
		Prem block Blob storage
			used for blobs
			supports lrs and zrs
		Prem Page blob storage
			used for page blobs i.e unmanaged vm disk (check this out)
			supports lrs 
			(e.g., IaaS disks).
		Prem file shares storage
			used for file sto (az files)
			supports lrs and zrs
			supports both smb (server message block)and nfs(network file system)

database
	every instance name must be unique (accessed directly by the url)
	data is stored wihtin blocks known as containers and items are the entries to them
	Databases are not cheaper, more secure, or more space efficient than other types of storage, but they are btter at sorting and indexing the data and also getting the op based on ip pref
	Cosmos DB
		its a PAAS offering 
		Very high availability throughout the globe 
		fast syncronization with other regions
		very expensive in nature
	Azure SQL
		its a PAAS offering 
		there are 2 services provided by az - az sql server and az sql managed instance
		az sql server is the traditinal sql on cloud
		az sql managed instance is the service generally used to migrate the data from on prem db instances to cloud db instances
		Azure SQL is a managed service, which means Microsoft takes care of all the hardware and maintenance tasks for running the database. You only have to worry about using the database for storing and retrieving data. There is no noticeable performance advantage with using either SQL service.
		 It is recommended by Azure to move your on-premises SQL Server instances to Azure SQL to improve efficiency and lower costs.
	Azure Mysql 
		its a paas (patching, monitoring, hardware and infra is managed)
		mysql is open src
		same all services as compared to az sql
	Az Postgresql
		similar to oabove ones
	Az sql Database server vs Az sql database
		need to check about it
	Data migration
		we can do data mig from on repm to Az sql Database server or Az sql database
	
Authorization and Authentication
	az active directory
		in the past there is a tool called ad active dir, which is used for managing resources on prem
		the idea of aad comes from that, the working of aad is entirely diff from ad
		Every azure user must have a aad service, its the first service(resource) to be invoked too
		for any first user, he must be present in a aad instance
		aad is a resource as others
		tenant is a dedicated instance of aad (its the first aad instance created when there is a new az user)
		tenant is generally used for aad services for the whole org
		each tenant is uniquely diff from other tenants and every user must be mapped to one single tenant, but they can be guest user of other tenants
		a user can be a member or guest of upto 500 tenants, but its tied to only one tenant
		subscription is the billing entity of the tenant, all the resources used by the usres comes under this subscription
		you can have multiple subscriptions to seperate the billing
		aad is generally used in hybrd mode, where it can manage resources and the entites that are present on prem and on the cloud
		ms entra is a new product , whihc includes aad, permissions management and verified id, which is used as auth2 service for multiple clouds
	zero trust concept
		there are 2 distinct security models used in orgs i.e perimter based security and zero trust per
		perimeter b s says that all the devices within the org's internal network which has certain physical boundaries can be trusted and all the other devices are not trusted
		in order to work remotely, you have to use vpn to extend the perimeter
		zero trust sec model says that the devices are authen based on identity but not the location.
		all the devices are not trusted until authen
		even if the device is authen, it has very minimal authoriz (permissions)
		the usage of aad and conditional policices can be used to manage the permi os the users
		now days, a hybrid model of 2 sec models is used
	Conditional access policy
		these are the extra set of rules used to enforce more author to the users
		basically, when you add a cond acc pol, you select a set of requirements (known as signals) and if the user matches all of them, you can either grant or block or grant with mfa access (known as decisions)
		examples
				enforce mfa for all users or all admins
				block signins with legacy auth proto
				add location based authen
				enforce authen only from managed devices
	Password less authentication
		its a combo of both convenience and high security, where we are authenticated to a service using mfa but wihtout passw
		there are 3 diff ways
			ms authenticator app
			windows hello - uses biometrics
			fido2 key - usb hardware key to be inserted
	External guest access
		aad takes care of inviting external users to the tenant
		these users are provided with min autho (can add cond acc pol too)
		also known as business to busi colloboration
	Azure active directory domain services- AADDS
		for legacy application which dont support modern auth protocols such as oauth2.0, its very hard to shift the app to cloud, as the aad is not very well supported
		these type of apps require ADDS (active direc domain service) which supports older auth proto such as ldap, kerberos or grp policies (ADDS is simply AD with managed services/proto as mentioned)
		there are many sols 
			maintain the on prem ad continuously and can sync with azure ad using a tool called azure ad connect (Not actually an solution but a option) - must be used only on the prem network
			create a adds (not the aadds) on the cloud vm, where we need to manage the os and maintenance seperately, this is also known as self managed ADDS
				This is referred to as self-managed AD, where you are in charge of configuring and maintaining a Windows Server acting as a domain controller. Self-managed AD on an Azure VM can use an existing domain namespace.
			AADDS is a Managed cloud hosted ADDS which provides all features of AD
		AADDS is Managed service (No OS or maintenece), behinf the scenes it uses 2 highly available microsoft domain controllers
		AADDS must have a unique address or url, as its not a extension of on prem ADDS and its standalone feature
		Azure AD DS is a fully managed instance of classic Active Directory which is compatible with AD DS managed domains and protocols such as NTLM, LDAP, Kerberos, Group Policy, and more. However, Azure AD DS requires a unique namespace and does not extend to an existing AD DS environment.
		AADDS can have 2 way sync with AAD and the on prem ADDS
	Azure active directory seamless single sign on
		AADSSSO is a feature that uses sso in aad
	
Az solutions
	iot
		iot hub
			paas solution which is low level solution for mananging the iot devices input data
		iot central
			apaas solution (Application platform as a service) which provides readymade dashboards for most used solutions
			can be thought as a high level solution for iot hub
		iot sphere
			its a iot device desined by ms for higher security and conn with az
			Azure Sphere consists of Microsoft-certified microcontrollers – single-chip computers with processors, storage, memory and IoT capabilities – plus the Azure Sphere Linux-based OS and the Azure Sphere cloud security service.
	Serverless
		serverless means that you dont have to manage any of the servers, your code runs on someone else server
		its extreme paas
		Serverless computing is the abstraction of servers, infrastructure, and operating systems. When you build serverless apps, you don't need to provision and manage any servers, so you don't have to worry about infrastructure. Serverless computing is driven by the reaction to events and triggers happening in near-real time in the cloud.
		az has different solutions for implementing serverless
			az functions
				this is the og serverless product used to compute just a function
				when you trigger the function, it performs the task and dies
			az logic app
				it is used to connect multiple applications (via templates or service endpoints) which a particular data flow pattern when triggered
				it can be used to automate long set of tasks
			az app service (present on the top)
				Azure App Service is a Platform as a Service (PaaS) offering that supports serverless application environments.
			az event grid
				this is like a Event routing platform, which routes any events produced from the source to the connected components (similar to kafka)
	devops
		az has many solution to implement the devops
		az devops suite
			consists of 5 internal tools
				az boards == jira
				az pipelines == jenkins
				az repos == github
				az test plans - performs autoomated testing during build
				az artifacts == jfrog
		az devtest labs
			specific service used, when we need manage multiple environments
			can use templates to perform deployments
		github and github actions

Security
	Defense in depth is the concept in which by layering different security measures, protection against security threats is greatly increased.
	there are 7 layers of secuirty followed in cloud hosting 
		Physical = restricted physical access to the data centers
		identity and access = aad which controls auth2
		perimeter = stops all type of protocol attacks
		network = filters the type of data coming in/ going out
		compute = protects from attackers coming into the vms
		gateways and firewalls = provides secuirty against all az appl
		data = the data in az is always encrypted (making unautho reads not possible)
	basic level of security
		firewall
			firewall is the most common security applied to the application, which defines certain rules for the incoming traffic to use the service, and blocks it if the rules are not met
			the firewall can be a software or hardware depending on the network size
		ddos protection service
			its a specific az service designed to mitigate the ddos attacks
			this service detects the ddos attack and defelects it from the service
			the level of security required can be configured
			coz of this service, no downtime of the service is ensured
		network security group - nsg
			it is like a resource firewall, where we can set a certain type of rules for the traffic through the required resources
			we can attach the nsg to entire vnet or its subnets or any particular network interface too
			we can have a main firewall for the entire application/ service, and within the internal architecture, we can also maintain multiple nsg groups for more security
		application security group - asg
			this is an extension to nsg, where we can group vms or vnets into different application groups and have a certain asg attached
			this type of firewall focuses on securing the entire application rather than a single endpoint
	public/service/private endpoints for Paas products
		public endpoints
			by default, when we create any kind of paas product such as az sql, az storage, etc, the service is publicly exposed i.ee it has a public endpoint (anyone on internet can access it using the public endpoint)
			when we contact the service from the vnet created, the vnet also uses the public endpoint for its communication (exposing the traffic to public)
		service endpoints
			these are the specific endpoints created within a subnet of vnet. these endpoints provide a private conn from the vnet to the services i.e data flows through the microsoft private network only, not through the internet
			we can also assign nsg or rules to the service to accept these service endpoints for much more better traffic screening
			these service endpoints are created only by the vnet, so when the on prem netw wants to access the service, they shld still use the public endpoints,(i.e the service now has to allow the public endpoints again for the on prem conn) exposing the traffic to server
			apart from this, these service end points provide the private conn to the service as a whole (not for a single instance within the service)
		private endpoints
			so, in order to completely eliminate the public access of the paas products, we can use private endpoints. these endpoints can be use by anyone i.e other vnets, on prem netw, etc without exposing the paas product
			so, for this, we intially create a managed network interface (imp to understand) within the vnet and a private link resource in the paas product, and link them both with the ms backbone network
			once this is done, we can simply expose this private ip addr (endpoint) to anyone
			this private endpoint also provide connection only to a specific instance of the paas product, ensuring more security
	microsoft defender for cloud
		its a internal ui tool within az to provide dashboard and mterics for security and regulatory complaince
		it comes with set of predefined rules to assess the arch of system, but we still have to add more rules for better assessment
	az key vault service
		key vault sevice is used to store passw and other secrets which can be used by third party appl by verifying themselves
		the hardware impl of az key vault is diff from others for more security
	az information protection service
		 this service is used to secure the data i.e emails, files, etc which is present outside of the organization, by classifying the users whether they have the read, r/w access based on the senstivity
		 Azure Information Protection helps secure email, documents, and sensitive data inside and outside your company walls. You can classify sensitive data, track activities on shared files and documents, collaborate securely, and much more. There is no active security service included, such as scanning the files being protected.
	ms defender for identity
		 this srvice is used to monitor the user, by checking the permissions, tracking the activities of the users and raise alarms
		 Microsoft Defender for Identity helps you detect and investigate security incidents across your Azure accounts, both on-premises and in the cloud. It monitors users, devices, and resources in terms of their behavior. If any behavior is out of the ordinary, an alarm can be raised.
	az sentinel
		sentinel is a siem tool security information and event management tool, which has 5 steps of working
			s1 data collection
			s2 aggr and normalization
			s3 analysis and threat detection
			s4 alarms raised if any
			s5 action taken
		all the 4 steps are done by sentinel, but only the 5th step must be taken by the admin
		it also uses behavorial analysis for the anamoly detection
	az dedicated hosts
		az also provides dedicated hardware for more strict compliance users, where the users are provisioned with dedicated hardware servers which are completely under the control of the user, making it more secure and little hard to maintain
		Azure dedicated hosts run on their own dedicated hardware inside the Azure datacenter and only your chosen VMs will run on it.

Monitoring and management
	Governance
		gov on az is a set of rules, policies and roles to define the acceptable use of resources.
		Governance describes being able to create and enforce standardized environments, usually to meet corporate or government requirements.
		Az Tags
			Tags are labels that can be attached to any Azure resource, whether they are individual services or even resource groups.
			tags can be attached to resource, res grps and subscription
			there is no inheritance of tags at all
			not all resources support the tags
		az policy
			this is a service which ensure that the policies created (set of rules) are in place during the usage of the resources
			generally used for resource manager
			A security policy defines the desired configuration of your workloads and helps ensure you're complying with the security requirements of your company or regulators.
		az rbac
			we can create multiple roles containing the perms and the access level of the res and provide these roles to multiple users based on the work they perform
			similar to iam tool
			3 components - role definition, security principle, scope
		az locks
			locks are the certain rules assigned to res or res grp or subscription, which are two types
				delete -  the item cant be deleted by anyone
				readonly - we cannot make any chgs to the item
					A read-only lock prevents both deletion AND modification of our locked resource.
			to perform any action, we must remove the locks intially and then perform the action
			generally usd for single resource
		az blueprints
			blueprints are used as the templates for creating the environement
			blueprints are a combo of
				resource templates
				rbac roles
				policies for the res
				compliance for authorities entities
		cloud adoption framework
			its like a framework for the org to move their services to the cloud
			its a collection of documents, gudiance principles and governance rules that need to be followed
			define strategy > plan > ready > adopt (migrate + innovate)
	az monitor
		az monitor is used for the telemetry of the services
		collects - metrics + logs
		this service is constanlty feeded with the data from the diff az services used and also the on prem services
		az monitor is centralized, and provides dashboard and can query the results
		we can use ml to understand the problems
		Azure Monitor includes services such as Log Analytics, Azure Monitor alerts, and Application Insights. Azure Monitor acts as a wide-ranging surveillance tool that aggregates, scrutinizes, and reacts to telemetry data from your cloud-based and on-site infrastructures. Log Analytics allows you to collect, store, and analyze log data from virtually any source, enabling in-depth insights into operational performances and patterns. Azure Monitor alerts help you identify and respond to critical situations and potential issues within your Azure resources, based on specific metrics and log query results. Application Insights monitors live applications, automatically detects performance anomalies, and helps in diagnosing issues and understanding how to improve app performance and usability.
		az log analytics
			used to analyze the logs of the az monitor
			az supports the queries in kql - kusto query lang
		az application insights
			provides the insights about the webapps
			works only for the web apps
			Application Insights provides website performance monitoring.
		az monitor alerts
			sends notifications incase of alarms
			Azure Monitoring alerts notify support personnel when there is an unexpected problem with your Azure environment, allowing a timely resolution.
			consists of 2 items
				alert rule
					conditions to trigger the alert, assigned a severity
				action grp
					actions taken when the alert is trigg
						email/sms
						automation workflows
	az service health
		free service to get the updates about the maintenance of the az services
		provides details whether these updates will your work
		notifies about the planned or unplanned outages
	Compliance
		general compliance policies 
			general data protection regulation
				protect indi and the processing of the data
				gives control back to the indi of the data, but not the cmpy
				Any company that wishes to interact with users located in the European Union must adhere to the many GDPR rules around privacy. The Microsoft Trust Center has more information on how to do this within Azure.
			iso standard
			nist - national inst of standards and technology
				famous example - cybersecuirty framework
		az compliance manager
			service that makes sure that services are compliant with all the above
			assign tasks to team mem
			provides a compliance score
			generate reports
	Privacy
		az information protection, az policies and other services provide the privacy for the cloud
		Privacy is a core component of each and every Azure service, so there isn't a single service. All products are built with privacy as a first-class citizen.
	Trust
		Trust center
			page for all links about privacy
		service trust portal
			The Service Trust Portal contains details about Microsoft's implementation of controls and processes that protect Microsoft's cloud services and the customers that use it.
	Az arc
		when we have multiple infra locations such as az, on prem and aws, it becomes hard to maintain the governance policies to them
		az arc is a centralized governance and management tool for az , on prem and other cloud res
		this works by installing a agent in the non az res, that converts the res into the az control plane
		Azure Arc can extend Azure RBAC controls to non-Azure resources.
		Azure Arc can extend the features and monitoring of Microsoft Defender for Cloud to non-Azure resources.
		Azure Arc can run some Azure serverless services on local/on-premises servers.

Saas products
	virtual desktop
		can virtulize apps and desktop
Pricing
	Subscriptions
		every resource come under a subscription
		each az acc can have multiple subscriptions
		cannot merge subscr, but move to another via support tkt
		it is the first thing that gets created
		The only way to combine Azure subscriptions is to open a support case with Microsoft. There is no such feature in the Azure Portal to combine Azure subscriptions. There is no such CLI command to merge subscriptions. There is no such PowerShell cmdlet either.
		A subscription is an agreement with Microsoft to use one or more Microsoft cloud platforms or services, for which charges accrue based on either a per-user license fee or on cloud-based resource consumption. Microsoft's Software as a Service (SaaS)-based cloud offerings (Office 365, Intune/EMS, and Dynamics 365) charge per-user license fees. Microsoft's Platform as a Service (PaaS) and Infrastructure as a Service (IaaS) cloud offerings (Azure) charge based on cloud resource consumption. Payment options can only be specified per Azure subscription. A reservation can be used to save costs on Azure resources. Azure resources, such as virtual machines, can be reserved in one-year or three-year terms, resulting in significant cost savings. However, Azure reservations are not used to manage billing for Azure services. If your organization has many subscriptions, you may need a way to efficiently manage access, policies, and compliance for those subscriptions. Azure management groups provide a level of scope above subscriptions. You organize subscriptions into containers called "management groups" and apply your governance conditions to the management groups. Billing is **not** managed via an Azure management group. There is no such thing as an Azure payment plan.
		An Azure subscription is associated with a billing account, and it acts as your billing boundary in your Azure environment.
	Management group
		Azure management groups provide a level of scope above subscriptions. You organize subscriptions into containers called management groups and apply your governance conditions to the management groups. For example, you can apply policies to a management group that limits the regions available for virtual machine (VM) creation.
		Management groups are an Azure resource management scope that sit above individual subscriptions. They are in fact a grouping, or collection of multiple subscriptions. Permissions, policies, and compliance settings applied to a management group are automatically inherited by all subscriptions inside of that group.
	Resource groups
		Resource groups are a management container inside of subscriptions, where groups of different Azure services are deployed. For this scenario, we want to use management groups, which are a higher-level, logical grouping of subscriptions.
	Cost Management portal
		its the portal that is used for the cost management for all services
		can provide optimizations too
		Azure Cost Management is a part of the Azure portal that can visualize your current and future costs. It also includes tools for financial governance to make sure you don't get unexpected costs from incorrect use of Azure resources. There are no discounts, gamification, or automatic shutdown services.
		spot vms
			when the az doesn't use its full computing capacity, it can lend out the compute capacity in the form of vms to the users at low costs, but the vms can be taken back at point of time
	Pricing factor/ Pricing Calculator
		the pricing of the services depend on various aspects such as the res size, type and bandwidth of the data
		the az regions are basically grouped into 3 billing zones; any data transfer within the billing zone is free (ingress) and any data flow from one billing zone to other (egress) is charged 
		The pricing calculator for Azure is a comprehensive tool you can use to estimate any combination of services on Azure. 
	TCO Calculator
		The Total Cost of Ownership Calculator can indicate the savings achieved by moving your on-premises services to Azure. The Azure portal can only estimate costs of existing services that you have in your account.
		total cost of ownership calculator is a specific type of calc which shows the amt saved by using az vs on prem infra
		The pricing calculator can create accurate estimates of hourly or monthly Azure costs across the entire Azure portfolio.
	Azure Advisor
		Advisor helps you optimize and reduce your overall Azure spending by identifying idle and underutilized resources.
		

			

Container
	all the dependencies of thw workload are present in the container img
	the cont img can be deployed easily to multiple different os and hardwares
	since the vm hosting the app requires a lot of maintainence such as for OS, etc, the cont img carry a less overhead, which doesnt conflict with the vm
	scaling and patchin  is easier


Misc
	To connect to a workspace from an environment outside of the workspace, you should download the config.json file for your workspace from the Azure portal. This includes the subscription and workspace information necessary to connect.



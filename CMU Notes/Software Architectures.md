- For architecting any system, we are initially handed few requirements i.e., Functional requirements.
	- Step 1: Converting the Functional requirements into Use cases (Known as Requirements Modelling)
	- Step 2: Converting the Use cases to Analysis Model (Known as Analysis Modelling)
	- Step 3: Converting the Analysis Model to Design Model (Known as Architecture and Design Modelling)
- On top of the Use cases from the functional requirements, we should also gather the "Quality Attributes"
- Quality attributes (known as Systemic properties) are the drivers that will be impacted by the architectural/ design decisions we make


- Architectural drivers are the requirements that shape the architecture. They are
	- Functional Requirements
	- Quality Attributes
	- Constraints
- Functional requirements
	- Simply put as "Capabilities" of the particular system
	- Captured in different ways such as
		- Use case
		- User stories
		- Feature descriptions
- Constraints
	- Simply put as "Have to adhere conditions"
	- Can be both technical as well as business constraints
	- Always preferred to document the constraints, so that we can explicitly mention them
- Quality Attributes
	- Characteristics that the system must possess apart from the functionality
	- There can be multiple quality attributes, and might require tradeoffs between them
	- Quality Attribute Scenario
		- Framework to describe the quality attributes, where we quantify them based on the scenarios
		- 6 parts of the framework
			- Source - Entity that generated the stimulus (External software)
			- Stimulus - Condition that effects the system (Payload which has malicious SQL statement)
			- Artifact - Which has got stimulated (Software got malfunctioned)
			- Environment - Condition under the stimulus occurred (Server is ready to respond)
			- Response - Activity in response to the stimulus (Detect and Recover/ Prevent)
			- Response measure - Measure with which the response is evaluated (Time logs of the entire scenario)
	- Modifiability
		- Refers to the ease with which a software system can accommodate a change
	- Performance
		- How long does it take to respond


- Context Diagram
	- Defines the boundary between the system, and its environment, showing the entities that interact with it
	- CD helps to understand the context and high level overview of the system, which can lead to better understanding of requirements from Architect's POV
	- Steps to build the Context diagram
		- Identify the Software system
		- Identify the Actors, that interact with the software system
		- Identify the high level interactions between the above 2 entities
	- CD is generally accompanied with other diagrams such as Event flow diagrams, Use case diagrams, etc.


- Software Architectural Designs
	- The design/ strcuture of the software impacts the properties the system will exhibit


- References
	- Software Architecture in Practice, 4th Edition - Len Bass
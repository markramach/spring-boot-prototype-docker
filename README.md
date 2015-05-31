##spring-boot-prototype-docker

Deploying a [Spring Boot](http://projects.spring.io/spring-boot) application in a container like [Docker](https://www.docker.com/) can dramatically simplify your deployment process. Using a containerized solution guarantees that your runtime environment is always in the state you want it in. As well as eliminating the chances of errors during deployments like missing or incorrect environment variables, missing dependencies, or ensuring that the server is running your preferred operating system. 

This prototype project demonstrates how you can build and package your application using Docker and the [Maven Docker Plugin](https://github.com/spotify/docker-maven-plugin) from Spotify.

### Pre-Requisites

This project requires the installation of Docker. See the Docker [installation](https://docs.docker.com/installation/#installation) guide to get up and running.

Once you have Docker installed, you will need to authenticate with the Docker Registry. To complete this step you will need to create an account [here](https://hub.docker.com/account/signup/)

Once you have created an account you should run the following command:

	docker login
	
You will be prompted for all authentication details.

### Quick Start

The easiest way to get started with the prototype is to fork, clone or download this repository.

	git clone https://github.com/markramach/spring-boot-prototype-docker.git
	
This prototype project consists of a simple spring boot REST service. To build the project simply run the following command:

	mvn clean install

### Dockerfile
	
At this point you will have a complete runnable Spring Boot jar file in the project's target directory. If you take a look at the [src/main/docker/Dockerfile](https://github.com/markramach/spring-boot-prototype-docker/blob/master/src/main/docker/Dockerfile) contained in the project, you will notice that this artifact is referenced in an `ADD` command. This command instructs docker to add the jar file to the container image.

	FROM java:8
	ADD spring-boot-prototype-docker.jar spring-boot-prototype-docker.jar
	ENTRYPOINT ["java","-jar","/spring-boot-prototype-docker.jar"] 
	
You will also notice that the first line of the Dockerfile indicates the image starts `FROM java:8`. This command indicates that the new image we are building will use the java:8 image as a base. The Docker registry provides you with a huge number of existing images to use when building your own. This is particularly helpful when your project requires a complex installation. Odds are someone has already created and image for it, so don't re-invent the wheel if you don't have to.

The final `ENTRY` command indicates that the image should run the `java -jar` command on startup.

For more information on Dockerfile commands see the reference documentation [here](http://docs.docker.com/reference/builder/).

### Build the Image
	
This project has the Maven Docker Plugin preconfigured in the pom.xml file. To build the docker image simply run the following command:

	mvn docker:build
	
The command should produce output similar to the following:

	[INFO]                                                                         
	[INFO] ------------------------------------------------------------------------
	[INFO] Building spring-boot-prototype-docker 1.0.0-SNAPSHOT
	[INFO] ------------------------------------------------------------------------
	[INFO] 
	[INFO] --- docker-maven-plugin:0.2.8:build (default-cli) @ spring-boot-prototype-docker ---
	[INFO] Copying /Users/mramach/projects_001/spring-boot-prototype-docker/target/spring-boot-prototype-docker.jar -> /Users/mramach/projects_001/spring-boot-prototype-docker/target/docker/spring-boot-prototype-docker.jar
	[INFO] Copying src/main/docker/Dockerfile -> /Users/mramach/projects_001/spring-boot-prototype-docker/target/docker/Dockerfile
	[INFO] Building image markramach/spring-boot-prototype-docker
	Step 0 : FROM java:8
	---> 64fb28226dbe
	Step 1 : ADD spring-boot-prototype-docker.jar spring-boot-prototype-docker.jar
	---> 1401f338b35e
	Removing intermediate container 8100000f57f9
	Step 2 : ENTRYPOINT java -jar /spring-boot-prototype-docker.jar
	---> Running in fa12c1afe74a
	---> 350c05d529c3
	Removing intermediate container fa12c1afe74a
	Successfully built 350c05d529c3
	[INFO] Built markramach/spring-boot-prototype-docker
	[INFO] ------------------------------------------------------------------------
	[INFO] BUILD SUCCESS
	[INFO] ------------------------------------------------------------------------
	[INFO] Total time: 2.633 s
	[INFO] Finished at: 2015-05-31T11:35:28-05:00
	[INFO] Final Memory: 22M/238M
	[INFO] ------------------------------------------------------------------------
	
If you run the `docker images` command, you should see that your image has been created on your local system.

	Marks-MacBook-Pro:spring-boot-prototype-docker mramach$ docker images
	REPOSITORY                                                  TAG                 IMAGE ID            CREATED              VIRTUAL SIZE
	markramach/spring-boot-prototype-docker                     latest              350c05d529c3        About a minute ago   830.4 MB	
	
### Tag the Image

Now that the image has been created, we should tag the image to indicate the version of the Spring Boot artifact that it contains. The prototype project has been configured to use the project version when tagging. To create the tag run the following command:

	mvn docker:tag
	
You should see output similar to the following:

	[INFO]                                                                         
	[INFO] ------------------------------------------------------------------------
	[INFO] Building spring-boot-prototype-docker 1.0.0-SNAPSHOT
	[INFO] ------------------------------------------------------------------------
	[INFO] 
	[INFO] --- docker-maven-plugin:0.2.8:tag (default-cli) @ spring-boot-prototype-docker ---
	[INFO] Creating tag markramach/spring-boot-prototype-docker:1.0.0-SNAPSHOT from markramach/spring-boot-prototype-docker
	[INFO] ------------------------------------------------------------------------
	[INFO] BUILD SUCCESS
	[INFO] ------------------------------------------------------------------------
	[INFO] Total time: 1.204 s
	[INFO] Finished at: 2015-05-31T11:40:40-05:00
	[INFO] Final Memory: 18M/312M
	[INFO] ------------------------------------------------------------------------
	
Running the `docker images` command should now produce the following output.

	Marks-MacBook-Pro:spring-boot-prototype-docker mramach$ docker images
	REPOSITORY                                                  TAG                 IMAGE ID            CREATED              VIRTUAL SIZE
	markramach/spring-boot-prototype-docker                     latest              350c05d529c3        6 minutes ago       830.4 MB
	markramach/spring-boot-prototype-docker                     1.0.0-SNAPSHOT      350c05d529c3        6 minutes ago       830.4 MB
	
Notice that both tags are pointed at the same image id.

### Push the Image

To make the image available outside of your local system, you need to push the image to a valid Docker Registry. This project pushes the images to a public repository provided by Docker. However, you could create a private repository on Docker. Or, host your own private registry. There are actually already several Docker private registry images available to choose from on [Docker Hub](https://registry.hub.docker.com/).

To push the new images to the repository run the following command:

	mvn docker:push

You should see output similar to the following:

	[INFO]                                                                         
	[INFO] ------------------------------------------------------------------------
	[INFO] Building spring-boot-prototype-docker 1.0.0-SNAPSHOT
	[INFO] ------------------------------------------------------------------------
	[INFO] 
	[INFO] --- docker-maven-plugin:0.2.8:push (default-cli) @ spring-boot-prototype-docker ---
	[INFO] Pushing markramach/spring-boot-prototype-docker
	The push refers to a repository [markramach/spring-boot-prototype-docker] (len: 2)
	Sending image list
	Pushing repository markramach/spring-boot-prototype-docker (2 tags)
	56629ecf9045: Image already pushed, skipping 
	1be9b9e79676: Image already pushed, skipping 
	62dcf9cfd2a5: Image already pushed, skipping 
	6967b36cd214: Image already pushed, skipping 
	edc01ba9bb37: Image already pushed, skipping 
	ddab90138bbd: Image already pushed, skipping 
	a36f74473d73: Image already pushed, skipping 
	85ad32c1110d: Image already pushed, skipping 
	398127e80e07: Image already pushed, skipping 
	0af13f0c614b: Image already pushed, skipping 
	64fb28226dbe: Image already pushed, skipping 
	1401f338b35e: Image successfully pushed 
	350c05d529c3: Image successfully pushed 
	Pushing tag for rev [350c05d529c3] on {https://cdn-registry-1.docker.io/v1/repositories/markramach/spring-boot-prototype-docker/tags/latest}
	Pushing tag for rev [350c05d529c3] on {https://cdn-registry-1.docker.io/v1/repositories/markramach/spring-boot-prototype-docker/tags/1.0.0-SNAPSHOT}
	[INFO] ------------------------------------------------------------------------
	[INFO] BUILD SUCCESS
	[INFO] ------------------------------------------------------------------------
	[INFO] Total time: 04:41 min
	[INFO] Finished at: 2015-05-31T11:54:02-05:00
	[INFO] Final Memory: 19M/313M
	[INFO] ------------------------------------------------------------------------

At this point you should be able to browse [Docker Registry](https://registry.hub.docker.com/u/markramach/spring-boot-prototype-docker/tags/manage/) and see 2 tag files.

### Start A Container

Now that the images have been pushed to the registry and the tags have been created, we can execute a `docker run` command and start the container from any host that has Docker installed. A prototype project container can be created and started using the following command:

	docker run -ti --name spring-boot-prototype-docker -p 8080:8080 markramach/spring-boot-prototype-docker

This command will start the container in the foreground and bind the container port 8080 to the local port 8080. This command does not include a tag name which means Docker will attempt to fetch the latest tag. You should see output similar to the following:

	Unable to find image 'markramach/spring-boot-prototype-docker:latest' locally
	Pulling repository markramach/spring-boot-prototype-docker
	350c05d529c3: Download complete 
	56629ecf9045: Download complete 
	1be9b9e79676: Download complete 
	62dcf9cfd2a5: Download complete 
	6967b36cd214: Download complete 
	edc01ba9bb37: Download complete 
	ddab90138bbd: Download complete 
	a36f74473d73: Download complete 
	85ad32c1110d: Download complete 
	398127e80e07: Download complete 
	0af13f0c614b: Download complete 
	64fb28226dbe: Download complete 
	1401f338b35e: Download complete 
	Status: Downloaded newer image for markramach/spring-boot-prototype-docker:latest
	
	  .   ____          _            __ _ _
	 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
	( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
	 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
	  '  |____| .__|_| |_|_| |_\__, | / / / /
	 =========|_|==============|___/=/_/_/_/
	 :: Spring Boot ::        (v1.2.3.RELEASE)
	
	2015-05-31 17:02:07.293  INFO 1 --- [           main] com.flyover.example.Application          : Starting Application v1.0.0-SNAPSHOT on a93e30f0afcd with PID 1 (/spring-boot-prototype-docker.jar started by root in /)
	2015-05-31 17:02:07.345  INFO 1 --- [           main] ationConfigEmbeddedWebApplicationContext : Refreshing org.springframework.boot.context.embedded.AnnotationConfigEmbeddedWebApplicationContext@3cd3450a: startup date [Sun May 31 17:02:07 UTC 2015]; root of context hierarchy
	2015-05-31 17:02:07.794  INFO 1 --- [           main] o.s.b.f.s.DefaultListableBeanFactory     : Overriding bean definition for bean 'beanNameViewResolver': replacing [Root bean: class [null]; scope=; abstract=false; lazyInit=false; autowireMode=3; dependencyCheck=0; autowireCandidate=true; primary=false; factoryBeanName=org.springframework.boot.autoconfigure.web.ErrorMvcAutoConfiguration$WhitelabelErrorViewConfiguration; factoryMethodName=beanNameViewResolver; initMethodName=null; destroyMethodName=(inferred); defined in class path resource [org/springframework/boot/autoconfigure/web/ErrorMvcAutoConfiguration$WhitelabelErrorViewConfiguration.class]] with [Root bean: class [null]; scope=; abstract=false; lazyInit=false; autowireMode=3; dependencyCheck=0; autowireCandidate=true; primary=false; factoryBeanName=org.springframework.boot.autoconfigure.web.WebMvcAutoConfiguration$WebMvcAutoConfigurationAdapter; factoryMethodName=beanNameViewResolver; initMethodName=null; destroyMethodName=(inferred); defined in class path resource [org/springframework/boot/autoconfigure/web/WebMvcAutoConfiguration$WebMvcAutoConfigurationAdapter.class]]
	2015-05-31 17:02:08.370  INFO 1 --- [           main] s.b.c.e.t.TomcatEmbeddedServletContainer : Tomcat initialized with port(s): 8080 (http)
	2015-05-31 17:02:08.619  INFO 1 --- [           main] o.apache.catalina.core.StandardService   : Starting service Tomcat
	2015-05-31 17:02:08.621  INFO 1 --- [           main] org.apache.catalina.core.StandardEngine  : Starting Servlet Engine: Apache Tomcat/8.0.20
	2015-05-31 17:02:08.738  INFO 1 --- [ost-startStop-1] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
	2015-05-31 17:02:08.739  INFO 1 --- [ost-startStop-1] o.s.web.context.ContextLoader            : Root WebApplicationContext: initialization completed in 1400 ms
	2015-05-31 17:02:09.560  INFO 1 --- [ost-startStop-1] o.s.b.c.e.ServletRegistrationBean        : Mapping servlet: 'dispatcherServlet' to [/]
	2015-05-31 17:02:09.564  INFO 1 --- [ost-startStop-1] o.s.b.c.embedded.FilterRegistrationBean  : Mapping filter: 'characterEncodingFilter' to: [/*]
	2015-05-31 17:02:09.564  INFO 1 --- [ost-startStop-1] o.s.b.c.embedded.FilterRegistrationBean  : Mapping filter: 'hiddenHttpMethodFilter' to: [/*]
	2015-05-31 17:02:09.827  INFO 1 --- [           main] s.w.s.m.m.a.RequestMappingHandlerAdapter : Looking for @ControllerAdvice: org.springframework.boot.context.embedded.AnnotationConfigEmbeddedWebApplicationContext@3cd3450a: startup date [Sun May 31 17:02:07 UTC 2015]; root of context hierarchy
	2015-05-31 17:02:09.872  INFO 1 --- [           main] s.w.s.m.m.a.RequestMappingHandlerMapping : Mapped "{[/say-hello],methods=[],params=[],headers=[],consumes=[],produces=[],custom=[]}" onto public org.springframework.http.ResponseEntity<java.util.Map<java.lang.String, java.lang.Object>> com.flyover.example.Application.sayHello(java.lang.String)
	2015-05-31 17:02:09.873  INFO 1 --- [           main] s.w.s.m.m.a.RequestMappingHandlerMapping : Mapped "{[/error],methods=[],params=[],headers=[],consumes=[],produces=[text/html],custom=[]}" onto public org.springframework.web.servlet.ModelAndView org.springframework.boot.autoconfigure.web.BasicErrorController.errorHtml(javax.servlet.http.HttpServletRequest)
	2015-05-31 17:02:09.874  INFO 1 --- [           main] s.w.s.m.m.a.RequestMappingHandlerMapping : Mapped "{[/error],methods=[],params=[],headers=[],consumes=[],produces=[],custom=[]}" onto public org.springframework.http.ResponseEntity<java.util.Map<java.lang.String, java.lang.Object>> org.springframework.boot.autoconfigure.web.BasicErrorController.error(javax.servlet.http.HttpServletRequest)
	2015-05-31 17:02:09.897  INFO 1 --- [           main] o.s.w.s.handler.SimpleUrlHandlerMapping  : Mapped URL path [/webjars/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
	2015-05-31 17:02:09.897  INFO 1 --- [           main] o.s.w.s.handler.SimpleUrlHandlerMapping  : Mapped URL path [/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
	2015-05-31 17:02:09.931  INFO 1 --- [           main] o.s.w.s.handler.SimpleUrlHandlerMapping  : Mapped URL path [/**/favicon.ico] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
	2015-05-31 17:02:10.001  INFO 1 --- [           main] o.s.j.e.a.AnnotationMBeanExporter        : Registering beans for JMX exposure on startup
	2015-05-31 17:02:10.072  INFO 1 --- [           main] s.b.c.e.t.TomcatEmbeddedServletContainer : Tomcat started on port(s): 8080 (http)
	2015-05-31 17:02:10.075  INFO 1 --- [           main] com.flyover.example.Application          : Started Application in 3.054 seconds (JVM running for 3.702)

Once the Spring Boot application is up and running you can execute a HTTP `GET` request on the sample resource. Depending on your docker setup localhost may not work. If you are running on OS X, you may need to use the IP address of the boot2docker VM.

	http://localhost:8080/say-hello?name=world
	
You should see the following response:

	{"hello":"world"}
	
### Plugin Configuration

			<plugin>
                <groupId>com.spotify</groupId>
                <artifactId>docker-maven-plugin</artifactId>
                <version>0.2.8</version>
                <configuration>
                    <imageName>${docker.image.prefix}/${project.artifactId}</imageName>
                    <dockerDirectory>src/main/docker</dockerDirectory>
                    <serverId>docker-hub</serverId>
                    <registryUrl>https://index.docker.io/v1/</registryUrl>
                    <image>${docker.image.prefix}/${project.artifactId}</image>
                    <newName>${docker.image.prefix}/${project.artifactId}:${project.version}</newName>
                    <forceTags>true</forceTags>
                    <resources>
                        <resource>
                            <targetPath>/</targetPath>
                            <directory>${project.build.directory}</directory>
                            <include>${project.build.finalName}.jar</include>
                        </resource>
                    </resources>
                </configuration>
            </plugin>
            
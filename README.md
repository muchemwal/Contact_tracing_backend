# Google & Apple COVID-19 exposure notification API backend

Using open source tools only for Contact tracing backend on Docker to equip a response centre to verify COVID-19 test results for users on the Notofocation API.

## The four main images for the backend are:

* The Verification Server (**verification-server**) helps validating whether upload requests from the mobile App are valid or not.
* The Verification Portal (**verification-portal**) allows callcentre employees to generate telePINs which are used by users of the mobile App to upload their diagnostic keys.
* The Verification Identity and Access  (**iam**) ensures that only authorized health personnel get access to the Verification Portal.
* The Test Result Server (**testresultserver**) receives the results from laboratories and delivers these results to the app via the verification-server.

## Starting the backend
You will start the Google & Apple COVID-19 exposure notification app backend with a single docker-compose file [docker-compose.yml](https://raw.githubusercontent.com/muchemwal/Contact_tracing_backend/master/docker-compose.yml)

Setup process:
* pull this repository
  * `<git pull https://github.com/muchemwal/Contact_tracing_backend.git>`.
* change into the new directory 
  * `<cd Contact_tracing_backend>`. 
* Add an entry to the hosts file of your docker host computer. Open /etc/hosts (Windows: C:\Windows\System32\drivers\etc\hosts) and add an entry "127.0.0.1 iam".
* Run `<docker-compose up>` 
  * if you used the docker-compose.yml in this repo it will pull images i built from my DockerHub - https://hub.docker.com/u/muchemwal
  * once all services are up run `docker ps --format '{{.Names}}\t{{.ID}}\t{{.Ports}}'` to verify, your results should be as below.
   
   ![ps image](/images/docker_ps.png)
  
* Open keycloak admin interface at http://iam:8080 with user admin, password admin
  * Create a new user. Go to Users -> Add User. Enter a username, click "Email verified".
  
    ![ps image](/images/add_user.png)
    
  * After clicking save, go to "Credentials" and add a password. Uncheck "temporary",  click "Set password".
  
    ![ps image](/images/credentials.png)
  
  * Go to "Role Mappings" in the user submenu and add the roles c19hotline and tele
  _generator.

    ![ps image](/images/roles.png)

__Verification Portal__

Open http://localhost:8081/cwa/teletan - this should redirect you to keycloak, where you can enter the credentials of the user that you manually created.
After that, you will be redirected back to the verification portal where you should be able to generate a telePIN.

* Generate PIN

  ![ps image](/images/generate_pin.png)
  
* Sample Generated PIN

  ![ps image](/images/pin.png)

__Verification Server__

You can see the swagger UI at this URL: http://localhost:8082/swagger-ui/

__Testresult Server__

To set a test result: try this:

curl -X POST -d "{\"id\":\"x\",\"result\":1}" -H "content-type:application/json" localhost:8083/api/v1/app/result
 * x should be a SHA256 string hash like "aa3cf384c9fb7dcc17bcefd6b43c0f1acaa53604998e7b5cbc3b41a9f349cd59" , 
 * You can use online generator like https://passwordsgenerator.net/sha256-hash-generator/ to generate a test string.
 

The **test_results_server** swagger is below.

  ![ps image](/images/test_result_swagger.png)

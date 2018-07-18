# Cocktail Installation Checklist

When Cocktail has been installed and environment setup has been completed, verify that the basic functions, such as service deployment, build, and deployment via template, are working properly.

1.Creating a Service

Deploy a simple Tomcat server as follows. When finished, connect to the corresponding service port and verify that the initial Tomcat screen is displayed normally. **Note that due to the nature of Tomcat, the memory request and limit amounts must be set to 512 and 1024, respectively, so that the system starts normally.**

Refer to the figure below and add a server from the Home > Service> Create Map > Create Server screen. Click the [Create] button.

![](/assets/deploy1.jpeg)

When the status is indicated as 'Running,' click 'webport:192.168...' under the Tomcat heading.

![](/assets/deploy2.jpeg)

If deployed properly, the initial Tomcat screen will be displayed as shown below. Note that Tomcat may initially require a considerable amount to start up.![](/assets/deploy3.jpeg)

2.Verifying Deployment via Catalogs

Check the template deployment feature by saving the Tomcat server as a template for others to deploy.

Click the [Save as Catalog] button from the Home > Service > Application Map > Deployment screen and register as shown below.

![](/assets/deploy4.jpeg)

![](/assets/deploy5.jpeg)

Verify the new deployment function using the completed Tomcat template.

Click the [Deploy] button from the Home > Catalog > Tomcat screen and enter the service, cluster, and application map information as shown below. Click the [Deploy] button.

If deployed properly, verify that the server can be accessed normally as in the previous Tomcat server deployment.

![](/assets/deploy6.jpeg)

3.Verifying Deployment via Source Build

Verify that an image can be created after creating a simple source build. Make sure the image is deployed properly.

Home > Service > Build Management![](/assets/deploy13.jpeg)

Click the [Add Build] button on the following screen and register as shown below. (Repository user ID/password: deploy/1234qwer. Be careful not to leak the login information.) 

![](/assets/deploy7.jpeg)

![](/assets/deploy8.jpeg)![](/assets/deploy9.jpeg)



![](/assets/deploy10.jpeg)

To verify that deployment is performed properly with a built image, select and deploy the image from the service registry and connect to the initial Tomcat page.



![](/assets/deploy11.jpeg)


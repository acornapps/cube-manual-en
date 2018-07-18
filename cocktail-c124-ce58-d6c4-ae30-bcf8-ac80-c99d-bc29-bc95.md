# Cocktail Installation Checklist

When Cocktail has been installed and environment setup has been completed, verify that the basic functions, such as service deployment, build, and deployment via template, are working properly.

**1.Deploy simple server**

Deploy a simple Tomcat server. When finished, connect to the corresponding service port and verify that the initial Tomcat screen is displayed normally. 
`Note`: For Tomcat, the memory request and limit amounts must be set to 512 and 1024, respectively, so that the system starts normally.

**2.Verifying Deployment via Catalogs**

Check the template deployment feature by saving the Tomcat server as a template for others to deploy.

**3.Verifying Deployment via Source Build**

Verify that an image can be created after creating a simple source build. Make sure the image is deployed properly.

To verify that deployment is performed properly with a built image, select and deploy the image from the service registry and connect to the initial Tomcat page.

For more information refer to cocktail user manual.




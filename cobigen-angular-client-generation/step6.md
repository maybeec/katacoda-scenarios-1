## Run the frontend server

Install the angular cli. By executing the following command a new terminal window will open.
`npm install -g @angular/cli`{{execute T2}}

The installer will ask you if you want to share anonymous usage data with the Angular Team at Google. You can negate this by tipping 'N'.
`N`{{execute T2}}

Navigate to the angular project and install the node dependencies.
`cd /root/devonfw/workspaces/main/cobigen-angular/devon4ng-application-template/`{{execute T2}}

`npm install`{{execute T2}}

The installer will ask again if you want to share data. Negate with 'N'.
`N`{{execute T2}}

Open the IDE and wait until it is completely loaded. Then open the file 'environment.ts' in the IDE.
`devonfw/workspaces/main/cobigen-angular/devon4ng-application-template/src/environments/environment.ts`{{open}}

Configure the paths to the backend server. For this change the properties 'restPathRoot' and 'restServiceRoot' with the values from the following code. Or simple click Copy to editor to copy the whole code into the file in the IDE.
<pre class="file" data-filename="devonfw/workspaces/main/cobigen-angular/devon4ng-application-template/src/environments/environment.ts">
export const environment = {
    production: false,
    restPathRoot: 'https://[[HOST_SUBDOMAIN]]-8081-[[KATACODA_HOST]].environments.katacoda.com/',
    restServiceRoot: 'https://[[HOST_SUBDOMAIN]]-8081-[[KATACODA_HOST]].environments.katacoda.com/services/rest/',
    security: 'jwt'
};
</pre>

Switch back to the second terminal to compile the angular app and run the frontend server.
`ng serve --host 0.0.0.0 --disable-host-check`{{execute T2}}

You can check out the application by opening the following link:
https://[[HOST_SUBDOMAIN]]-4200-[[KATACODA_HOST]].environments.katacoda.com/

Login with username 'admin' and password 'admin'.

The Application shows a simple web page with a navigation bar on the left side. This navigation bar contains a home section and a sample data section. The sample data section will not work because it is not supported by the backend server. In the next step we will change this part to display the employee data with the newly generated component by CobiGen.
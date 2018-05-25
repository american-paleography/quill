How to get T-Pen built, deployed, and running for a Linux-savvy techie who doesn't know the Java ecosystem.

== Building

Input: source code, IDE, and a `version.properties` file
Output: .war file

1. Code is available at https://sourceforge.net/p/newberrytpen/git/ci/master/tree/ - use the `update` branch (currently), _not_ a branch that says "Deploy"
2. Acquire a suitable `version.properties` template file, and customize it appropriately. Place this inside the correct folder; .gitignore has a reference to where this is.
3. Install Netbeans (a Java-oriented IDE) locally. There's no CLI build script yet, so we'll use the same IDE as upstream.
4. Create a new project, using the "Java Web" project category and the "Web Application with Existing Sources".
5. Fill in the git repo's worktree root directory when asked where existing sources are. Probably easiest if you use the same directory as the project/build dir as well; build.xml can be deleted.
6. From the "Run" menu, select the "Build Project" option.
7. If this creates a `dist` folder with a .war file somewhere in it, success! If not, then there may be an issue with libraries not having been supplied successfully.

== Deployment/Running

Input: .war file, Linux VPS (such as EC2 server running Amazon's distro)
Output: application running over HTTP on port 8080; and Tomcat webserver instance running (it manages Java server-side webapps)

All paths and package names assume Amazon's EC2 distro. If a step solely consists of an inline code block, run that in bash (should be self-explanatory).

1. Copy the .war file (see "Building") onto the server.
2. `sudo yum install tomcat7 tomcat7-webapps`
3. Note: the `$CATALINA_HOME` directory (if you need to google things) appears to be `/usr/share/tomcat7`, and you'll need to use the `webapps` folder.
4. You might need a newer Java (jdk) version, such as if Netbeans built the .war for Java 8. If this is the case, run something like `sudo yum install java-1.8.0`, and _then_ run `sudo yum remove java-1.7.0-openjdk` (install-then-remove will avoid having other packages uninstalled).
5. Put the .war file into `/usr/share/tomcat7/webapps`, and restart Tomcat (`sudo service tomcat7 restart`).
6. If successful, the app should now be running on port `$host:8080/$app_path`.

Troubleshooting:
If the final step above was NOT successful, a decent troubleshooting approach seems to be making use of the manager webapp, plus the tomcat logs.

The manager webapp is a separate package on Amazon's distro, so install that with `sudo yum install tomcat7-admin-webapps`. You'll have to enable an admin user in `$CATALINA_HOME/conf/tomcat-users.xml` as well.

Logs are in `$CATALINA_HOME/logs`. If the important log entry makes reference to a "container-specific log file" (or similar), that's in the _same_ directory - a `tail -n0 -f *` works decently.

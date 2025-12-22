# Development Guide

## Building the Plugin

The project now builds with Java 8-25 using public Maven repositories:

```bash
cd oslcjira
mvn clean package
```

This produces `target/oslcjira-1.0.jar` (Atlassian plugin JAR).

## Running JIRA with the Plugin (No Installation Required!)

The Atlassian Maven Plugin (AMPS) can download and run JIRA automatically with your plugin installed:

### Quick Start - Run JIRA with your plugin:

```bash
cd oslcjira
echo "n" | mvn jira:run
```

Or if you prefer to run it interactively:
```bash
mvn jira:run
# When prompted about the mailing list, type 'n' and press Enter
```

This will:
1. Download JIRA 6.3.6 (configured version) automatically (~120MB download first time)
2. Install your plugin
3. Start JIRA on http://localhost:2990/jira
4. Set up a test environment with sample data

**Login credentials:** admin/admin

**Note:** The first run takes 5-10 minutes to download JIRA and initialize. Subsequent runs are much faster.

### Useful AMPS Commands

```bash
# Run JIRA with the plugin
mvn jira:run

# Debug mode (listens on port 5005)
mvn jira:debug

# Run in standalone mode (faster startup, no test data)
mvn jira:run -Dhttp.port=8080

# Stop the running instance
mvn jira:stop

# Clean JIRA home directory (resets data)
mvn jira:clean

# Install plugin to already running JIRA
atlas-install-plugin
```

### Development Workflow

1. **Initial run:**
   ```bash
   mvn jira:run
   ```
   Wait for "jira started successfully" message

2. **Access JIRA:**
   - URL: http://localhost:2990/jira
   - Login: admin/admin

3. **Make code changes:**
   - Edit Java files
   - Keep JIRA running

4. **Test changes with FastDev:**
   - FastDev (QuickReload) is enabled by default
   - Run `atlas-package` or `mvn package` in another terminal
   - The plugin reloads automatically in the running JIRA instance
   - Or use `atlas-mvn jira:run -DenableQuickReload=true` (already default)

### Debug Mode

To debug the plugin with your IDE:

```bash
mvn jira:debug
```

Then attach your IDE debugger to `localhost:5005`.

**IntelliJ IDEA:**
1. Run → Edit Configurations → Add New → Remote
2. Host: localhost, Port: 5005
3. Click Debug

**VS Code:**
Add to `.vscode/launch.json`:
```json
{
  "type": "java",
  "name": "Attach to JIRA",
  "request": "attach",
  "hostName": "localhost",
  "port": 5005
}
```

### Configuration

The AMPS settings are in `pom.xml`:

```xml
<properties>
    <jira.version>6.3.6</jira.version>
    <amps.version>5.0.4</amps.version>
</properties>
```

To test with a different JIRA version:
```bash
mvn jira:run -Djira.version=7.13.0
```

### Troubleshooting

**Port already in use:**
```bash
mvn jira:run -Dhttp.port=8090
```

**Out of memory:**
```bash
export MAVEN_OPTS="-Xmx2g -XX:MaxMetaspaceSize=512m"
mvn jira:run
```

**Clean start (removes all data):**
```bash
mvn jira:clean jira:run
```

### OSLC Plugin Testing

Once JIRA is running with your plugin:

1. Navigate to **Administration** → **Add-ons** → **Manage add-ons**
2. Find "JIRA OSLC Provider" in the list
3. Check that it's enabled
4. Access OSLC endpoints:
   - Root services: http://localhost:2990/jira/rest/oslc-cm/rootservices
   - Service provider catalog: http://localhost:2990/jira/rest/oslc-cm/catalog

## More Information

- [Atlassian Plugin SDK Documentation](https://developer.atlassian.com/server/framework/atlassian-sdk/)
- [AMPS Plugin Goals](https://developer.atlassian.com/server/framework/atlassian-sdk/amps-maven-goals-reference/)

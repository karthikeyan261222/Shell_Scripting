# GitHub Repository Read Access Checker

This Bash script checks and lists users with **read (pull) access** to a given GitHub repository using the **GitHub REST API**.

## 📌 Features

* Uses **GitHub REST API** to fetch collaborators.
* Filters users who have **read access** (`pull = true`).
* Lightweight and easy to run.

## ⚙️ Prerequisites

Before running the script, ensure you have:

1. **GitHub Personal Access Token (PAT)**
   * Go to GitHub Settings → Developer settings → Personal Access Tokens.
   * Generate a new token with at least:
     * `repo` (Full control of private repositories) OR
     * `read:org` (if working with organization repos).

2. **Dependencies Installed**
   * `curl` (for API calls)
   * `jq` (for JSON parsing)

Install on Linux:

```bash
sudo apt-get update && sudo apt-get install curl jq -y
```

## 🚀 Setup

1. **Clone this repository** or download the script.

   ```bash
   git clone https://github.com/your-username/repo-access-checker.git
   cd repo-access-checker
   ```

2. **Make the script executable**

   ```bash
   chmod +x github-read-access-checker.sh
   ```

3. **Set your GitHub credentials**
   Export your GitHub username and token as environment variables:

   ```bash
   export username="your-github-username"
   export token="your-personal-access-token"
   ```

## ▶️ Usage

Run the script with repository owner and name:

```bash
./github-read-access-checker.sh <repo_owner> <repo_name>
```

### Example

```bash
./github-read-access-checker.sh octocat hello-world
```

### Output

```bash
Listing users with read access to octocat/hello-world...
Users with read access to octocat/hello-world:
user1
user2
user3
```

If no users have read access, it will display:

```bash
No users with read access found for octocat/hello-world.
```

## 📜 Script Content

```bash
#!/bin/bash
# -----------------------------------------------------------------------------
# Name        : GitHub Repository Read Access Checker
# Version     : 1.0
# Created on  : 17.08.2025
# Created by  : Karthikeyan
# Description : This script checks and lists users with read (pull) access to a
#               given GitHub repository using the GitHub REST API.
# -----------------------------------------------------------------------------

# GitHub API URL
API_URL="https://api.github.com"

# GitHub username and personal access token
USERNAME=$username
TOKEN=$token

# User and Repository information
REPO_OWNER=$1
REPO_NAME=$2

# Function to make a GET request to the GitHub API
function github_api_get {
    local endpoint="$1"
    local url="${API_URL}/${endpoint}"
    
    # Send a GET request to the GitHub API with authentication
    curl -s -u "${USERNAME}:${TOKEN}" "$url"
}

# Function to list users with read access to the repository
function list_users_with_read_access {
    local endpoint="repos/${REPO_OWNER}/${REPO_NAME}/collaborators"
    
    # Fetch the list of collaborators on the repository
    collaborators="$(github_api_get "$endpoint" | jq -r '.[] | select(.permissions.pull == true) | .login')"
    
    # Display the list of collaborators with read access
    if [[ -z "$collaborators" ]]; then
        echo "No users with read access found for ${REPO_OWNER}/${REPO_NAME}."
    else
        echo "Users with read access to ${REPO_OWNER}/${REPO_NAME}:"
        echo "$collaborators"
    fi
}

# Main script
echo "Listing users with read access to ${REPO_OWNER}/${REPO_NAME}..."
list_users_with_read_access
```

## 📖 Script Overview

* **github_api_get** → Handles authenticated requests to the GitHub API.
* **list_users_with_read_access** → Fetches collaborators and filters only those with `pull` access.

## ⚠️ Notes

* Make sure your token has **sufficient permissions** to access private repositories.
* For public repositories, read access is available to everyone, but this script only lists collaborators.
* If you hit **rate limits**, consider increasing scope or using a higher-permission token.

## 🔧 Troubleshooting

### Common Issues

1. **Authentication Failed**
   ```bash
   # Verify your credentials are set correctly
   echo $username
   echo $token
   ```

2. **Missing Dependencies**
   ```bash
   # Check if curl and jq are installed
   which curl jq
   ```

3. **API Rate Limit**
   ```bash
   # Check your current rate limit status
   curl -s -u "$username:$token" https://api.github.com/rate_limit
   ```


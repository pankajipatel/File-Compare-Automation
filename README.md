# File-Compare-Automation
Files migrated to content server need to be verified as part of the data migration process.
# -*- coding: utf-8 -*-
"""
Created on Thu Aug 14 14:32:45 2025

@author: patelpi
"""

import requests

def download_file_from_content_server(base_url, object_id, auth_token, local_file_path):
    """
    Downloads a file from a Content Server using its RESTful API.

    :param base_url: The base URL of the Content Server API.
    :param object_id: The object ID of the file to download.
    :param auth_token: The authentication token for accessing the API.
    :param local_file_path: The local path where the file will be saved.
    """
    # Construct the URL for the file download endpoint
    download_url = f"{base_url}/api/v1/files/{object_id}/download"

    # Set up the headers for the request, including the authentication token
    headers = {
        'Authorization': f'Bearer {auth_token}',
        'Accept': 'application/octet-stream'
    }

    try:
        # Make the request to download the file
        response = requests.get(download_url, headers=headers, stream=True)

        # Check if the request was successful
        if response.status_code == 200:
            # Open a local file in write-binary mode and write the content to it
            with open(local_file_path, 'wb') as local_file:
                for chunk in response.iter_content(chunk_size=8192):
                    local_file.write(chunk)
            print(f"File downloaded successfully and saved to {local_file_path}")
        else:
            print(f"Failed to download file. Status code: {response.status_code}")
            print(f"Response: {response.text}")

    except requests.exceptions.RequestException as e:
        print(f"An error occurred: {e}")

# Example usage
if __name__ == "__main__":
    # Replace these values with your actual configuration
    BASE_URL = "https://your-content-server.com"
    OBJECT_ID = "12345"  # The object ID of the file you want to download
    AUTH_TOKEN = "your-auth-token"  # Your authentication token
    LOCAL_FILE_PATH = "downloaded_file.ext"  # Path where the file will be saved locally

    download_file_from_content_server(BASE_URL, OBJECT_ID, AUTH_TOKEN, LOCAL_FILE_PATH)

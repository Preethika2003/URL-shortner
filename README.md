# URL-shortner
#shorten url
!pip install requests
import requests
import json
import os
from google.colab import files

FILE_NAME = "shortened_links.json"

def shorten_url(long_url):
    url = f"https://tinyurl.com/api-create.php?url={long_url}"

    response = requests.get(url)

    if response.status_code == 200:
        short_link = response.text
        save_link(long_url, short_link)
        return short_link
    else:
        print(f"Error: {response.status_code}")
        return None

def save_link(long_url, short_url):
    links = {}
    if os.path.exists(FILE_NAME):
        with open(FILE_NAME, "r") as file:
            try:
                links = json.load(file)
            except json.JSONDecodeError:
                pass

    links[long_url] = short_url

    with open(FILE_NAME, "w") as file:
        json.dump(links, file, indent=4)

    print("Link saved successfully!")
    files.download(FILE_NAME)

def retrieve_links():
    if os.path.exists(FILE_NAME):
        with open(FILE_NAME, "r") as file:
            try:
                links = json.load(file)
                if links:
                    for long_url, short_url in links.items():
                        print(f"{long_url} -> {short_url}")
                else:
                    print("No stored links found!")
            except json.JSONDecodeError:
                print("Error reading stored links file.")
    else:
        print("No stored links found!")

if __name__ == "__main__":
    print("1. Shorten a URL")
    print("2. Retrieve stored links")
    choice = input("Choose an option (1/2): ")

    if choice == "1":
        long_url = input("Enter the long URL to shorten: ")
        short_url = shorten_url(long_url)
        if short_url:
            print(f"Shortened URL: {short_url}")
    elif choice == "2":
        retrieve_links()
    else:
        print("Invalid choice. Exiting.")

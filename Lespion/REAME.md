# ⭐CyberDefenders - Lespion Lab Writeup⭐

![CyberDefenders](https://img.shields.io/badge/CyberDefenders-Threat%20Intel-blue)
![Difficulty](https://img.shields.io/badge/Difficulty-Easy-green)
![Category](https://img.shields.io/badge/Category-Threat%20Intelligence-red)
![Tools](https://img.shields.io/badge/Tools-Sherlock%2C%20TinEye%2C%20CyberChef-orange)

## Lab Info

| Property | Value |
|----------|-------|
| **Platform** | CyberDefenders |
| **Category** | SOC Analyst Tier 1 - Level 1 |
| **Difficulty** | Easy |
| **Lab Type** | Threat Intelligence |
| **Objective** | Investigate an insider threat by analyzing GitHub repositories for exposed credentials, using OSINT tools to correlate online accounts, and performing image analysis to identify locations. |

---

## Tools Used
- Google Maps
- Google Dorks
- Google Image Search
- TinEye
- Sherlock
- CyberChef

## Scenario
You have been tasked by a client whose network was compromised and brought offline to investigate the incident and determine the attacker's identity.

Incident responders and digital forensic investigators are currently on the scene and have conducted a preliminary investigation. Their findings show that the attack originated from a single user account, probably an insider. Investigate the incident, find the insider, and uncover the attack actions.

---

## Challenge Questions & Solutions

### Question 1: What API key did the insider add to his GitHub repositories?

**File:** `Github.txt`

**Steps:**
1. Download and extract the provided files
2. The text file contains a GitHub link: `https://github.com/EMarseille99`
3. Additionally, we have two images: `office.jpg` and `WebCam.png`
4. The GitHub profile shows 14 repositories
5. Navigate to the repository: `Project-Build---Custom-Login-Page`
6. Open the file `/Login Page.js`
7. The API key is exposed in the first line of code

**Answer:** `aJFRaLHjMXvYZgLPwiJkroYLGRkNBW`

---

### Question 2: What plaintext password did the insider add to his GitHub repositories?

**File:** `Github.txt`

**Steps:**
1. In the same file (`Login Page.js`), we find a Base64-encoded password in the code:
```html
<div class="wrap-input100 validate-input" data-validate = "Password is required">
```

2. The encoded password found: `UGljYXNzb0JhZ3VldHRlOTk=`
3. Decode using CyberChef: https://gchq.github.io/CyberChef/
4. Use the "From Base64" recipe

**Answer:** `PicassoBaguette99`

---

### Question 3: What cryptocurrency mining tool did the insider use?

**File:** `Github.txt`

**Steps:**
1. Search through the insider's GitHub repositories
2. Search for the term "crypto" in the portfolio
3. Among all repositories, we find one called `XMRIG`
4. XMRIG is a well-known cryptocurrency mining software

**Answer:** `XMRIG`

---

### Question 4: On which gaming website did the insider have an account?

**Steps:**
1. Use Sherlock OSINT tool to find online accounts associated with the username
2. Install Sherlock:
```bash
git clone https://github.com/sherlock-project/sherlock.git
cd sherlock
python3 -m venv sherlock-env
source sherlock-env/bin/activate
pip install sherlock-project
```

3. Run the search:
```bash
sherlock EMarseille99 --print-found
```

4. Results obtained:
```
[+] Envato Forum: https://forums.envato.com/u/EMarseille99
[+] GitHub: https://www.github.com/EMarseille99
[+] Steam Community (User): https://steamcommunity.com/id/EMarseille99/
[+] YouTube: https://www.youtube.com/@EMarseille99
```

**Answer:** `Steam`

---

### Question 5: What is the link to the insider's Instagram profile?

**Steps:**
1. Sherlock did not return Instagram results
2. Use Google Dorks to find the profile:
```
site:instagram.com EMarseille99
```

3. This search reveals the Instagram profile

**Answer:** `https://www.instagram.com/emarseille99/`

---

### Question 6: Which country did the insider visit on her holiday?

**Steps:**
1. Investigate the Instagram profile posts
2. Find a post with the caption: "Once in a lifetime holiday here, love me some slings x"
3. The keyword "holiday" indicates this is the vacation location
4. Perform reverse image search on the photo
5. The image shows distinctive architecture and landmarks

**Answer:** `Singapore`

---

### Question 7: Which city does the insider's family live in?

**Steps:**
1. Continue investigating the Instagram profile
2. Find an image with the caption: "Nice to meet friends & family Photo 1/2"
3. This caption indicates it's the city where their family lives
4. The image shows a house with a recognizable landmark/flag in the background
5. The flag visible in the image is from Dubai

**Answer:** `Dubai`

---

### Question 8: Which city is the company located in?

**File:** `office.jpg`

**Steps:**
1. Analyze the provided image of the building where the company has an office
2. While reverse image search could be used, we'll use visual analysis instead
3. In the background, there's a shopping center called "Grand Central"
4. The street signs show directions, written in English
5. One sign mentions "Chinese Quarter"
6. Search Google Maps for "Chinese Quarter" - options appear in Paris and Birmingham
7. Since all street signage is in English, Birmingham is the likely location
8. Navigate in Google Maps from "Chinese Quarter" to "Grand Central" in Birmingham
9. Use Street View to verify the perspective matches the photograph

**Answer:** `Birmingham`

---

### Question 9: Which state is this camera in?

**File:** `Webcam.png`

**Steps:**
1. The surveillance team followed the target to an airport
2. The target's plane landed in another country
3. Intelligence spotted the target via an IP camera
4. While Google reverse image search could be used, we'll use an alternative method
5. Use TinEye for reverse image search: https://tineye.com/
6. Upload `Webcam.png` to TinEye
7. Review the results, paying attention to:
   - Oldest results (likely original source)
   - Domains where the image appears
   - Any location metadata in the hosting pages
8. Cross-reference the results to identify the state

**Answer:** `Indiana`

---

## Key Takeaways

This lab demonstrates essential OSINT techniques:
- **GitHub reconnaissance:** Finding exposed credentials in public repositories
- **Username enumeration:** Using tools like Sherlock to find associated accounts
- **Social media investigation:** Gathering intelligence from Instagram profiles
- **Image analysis:** Using both reverse image search and manual geolocation techniques
- **Google Dorks:** Leveraging search operators to find specific information

## Tools Summary

| Tool | Purpose |
|------|---------|
| Sherlock | Username enumeration across multiple platforms |
| CyberChef | Decoding Base64 and other encoding schemes |
| Google Dorks | Targeted searches using advanced operators |
| TinEye | Reverse image search (alternative to Google) |
| Google Maps | Geolocation and verification |

---

**Lab completed successfully - All questions answered using various OSINT methodologies.**

\# MedVitals AI — Patient Data Assistant \# 1 

\# Configuration file · Version 1.0 \# 2 

import requests \# 3 

import os \# 4 

 \# 5 

\# \============================================ \# 6 

\# SECTION 1: API CREDENTIALS \# 7 

\# \============================================ \# 8 

\# RED FLAG 1 — Hardcoded Secrets (all three lines below) \# 9 AI\_MODEL\_KEY \= "sk-medvitals-prod-8f2a91bc34de" \# 10 

DATABASE\_URL \= "postgresql://admin:Hospital@2024\!@db.medvitals.ng/patients" \# 11 INTERNAL\_TOKEN=Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9" \# 12  \# 13 

ANSWERS

Flag 1 \- Hardcoded secrets

RISK ;In Section1 no 10,11,12 any one can get the API keys ,DB passwords and internal token in plain text

Fix;load each value with os.environ.get(),and keep the real values in a .env file

Flag 2 Wildcard Permission

Risk;in section 2 IAM\_POLICY={“effect”;”Action”}from line 19,20,21

If the credentials in the policy are stolen ,the attacker has full control of the cloud account

FIX:assign action and resource to only what the user needs e.g s3 GetObject on one bucket

Flag 3 missing environment variable

Risk ;in Section 4 at the startup sequence

From line 48 to 51,the start of sequence would expose the code

FIX ;remove secret value from all print /log statement 

\# \============================================ \# 14 

\# SECTION 2: CLOUD PERMISSIONS \# 15 

\# \============================================ \# 16 

\# RED FLAG 2 — Wildcard Permissions \# 17 

IAM\_POLICY \= { \# 18 

 "Effect": "Allow", \# 19 

 "Action": "\*", \# Allows EVERYTHING \# 20 

 "Resource": "\*" \# On EVERYTHING in the account \# 21 

} \# 22 

 \# 23 

\# \============================================ \# 24 

\# SECTION 3: AI MODEL FUNCTION \# 25 

\# \============================================ \# 26 

def get\_patient\_summary(patient\_id): \# 27 

 headers \= {"Authorization": f"Bearer {AI\_MODEL\_KEY}"} \# 28  response \= requests.post( \# 29 

 "https://api.openai.com/v1/chat/completions", \# 30 

 headers=headers, \# 31 

 json={ \# 32 

 "model": "gpt-4", \# 33 

 "messages": \[{ \# 34 

 "role": "system", \# 35 

 "content": "You are a medical AI assistant." \# 36  }, { \# 37 

 "role": "user", \# 38 

 "content": f"Summarise record for: {patient\_id}" \# 39  }\] \# 40 

 } \# 41 

 ) \# 42 

 return response.json() \# 43 

 \# 44  
\# \============================================ \# 45 

\# SECTION 4: STARTUP SEQUENCE \# 46 

\# \============================================ \# 47 

\# RED FLAG 1 (again) — Printing secrets to terminal logs \# 48 print(f"Connected to: {DATABASE\_URL}") \# Logs the full DB password \# 49 print(f"AI Key: {AI\_MODEL\_KEY}") \# Logs the API key \# 50 print("System ready.") \# 51

ANSWERS

Flag 1 \- Hardcoded secrets

RISK ;In Section1 no 10,11,12 any one can get the API keys ,DB passwords and internal token in plain text

Fix;load each value with os.environ.get(),and keep the real values in a .env file

Flag 2 Wildcard Permission

Risk;in section 2 IAM\_POLICY={“effect”;”Action”}from line 19,20,21

If the credentials in the policy are stolen ,the attacker has full control of the cloud account

FIX:assign action and resource to only what the user needs e.g s3 GetObject on one bucket

Flag 3 missing environment variable

Risk ;in Section 4 at the startup sequence

From line 48 to 51,the start of sequence would expose the code

FIX ;remove secret value from all print /log statement 
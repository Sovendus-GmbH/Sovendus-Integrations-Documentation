# Connect Emarsys

## 📝 Steps to Connect Emarsys

1. **Create an API key**
   - Follow the instructions provided [here](https://dev.emarsys.com/docs/core-api-reference/a12e08c686f0f-2-set-up-your-account).
   - For the initial setup phase, ensure permissions to read/write contacts, read contact lists, and fields are granted.
   - Use the API to look up the field IDs to craft your API request configuration.

2. **Create a list of Field Names**
   - Identify the fields you want to populate for each new account.
   - Provide example values for each field (screenshots from an example contact are acceptable).

3. **Temporarily create a user for Sovendus**
   - Allow Sovendus to access your Emarsys dashboard to verify if the test contact has any issues.

4. **Get the contact list ID**
   - Identify the contact list where the leads should be added.

5. **Send the required information to Sovendus**
   - Compile the following details:
     - Newsletter System name
     - API key
     - Field names with example values
     - Contact list ID

6. **Adjust API key permissions**
   - Once Sovendus confirms the setup is correct, adjust the API key permissions to "contacts read" only.
   - Delete the Sovendus user as backend access is no longer required.

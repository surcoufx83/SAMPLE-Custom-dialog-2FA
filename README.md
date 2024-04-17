# SAMPLE-Custom-dialog-2FA

## Requirements
- sysHUB Server 2023.2.0 or newer
- Node.JS (with npm included) LTS (20.x) or newer

## Installation
1. Download the [ppk file](https://github.com/surcoufx83/SAMPLE-Custom-dialog-2FA/raw/main/SAMPLE%20Custom%20dialog%202FA%201.0.ppk) and import it via webclient -> Transport -> Import package
2. If not yet done, [install Node.JS](https://nodejs.org/en/download)
3. Open command prompt and check that `npm -v` is working and outputs a version number. If not return to step 2.
4. In the command prompt navigate to the `tools` folder within sysHUB root. If `tools` does not exist, create it. In my environment sysHUB is located at `D:\sysHUB\2023.2.0`, so I run the following commands:
   ```
   d:
   cd D:\sysHUB\2023.2.0\tools
   ```
5. Inside the `tools` folder create a folder called `npm`, navigate into it and initialize a new npm folder:
   ```
   mkdir npm
   cd npm
   npm init
   // comment: answer all inputs with enter key
   ```
6. Install the `authenticator` package which is used for creating otp key and qr-code data:
   ```
   npm i authenticator-cli
   ```
7. Validate in Explorer that the folder `D:\sysHUB\2023.2.0\tools\npm\node_modules\.bin` exists and inside a file called `authenticator.cmd`.
8. Test the authenticator by simply running the `D:\sysHUB\2023.2.0\tools\npm\node_modules\.bin\authenticator.cmd`. This should give you three output lines, a key, a token and and url. If so, everything is prepared. Example output:
   ```
   Key: 6kvt ursx oymh slsa 5ysn 65fc aahf 3zjd
   Token: 070247
   URL: otpauth://totp/ACME:user@example.com?secret=6KVTURSXOYMHSLSA5YSN65FCAAHF3ZJD&issuer=ACME&algorithm=SHA1&digits=6&period=30
   ```
9.  If you use a different folder than `{rootdir}\tools\npm\` you must update the parameterset nodes `TOTP/binDir` and `TOTP/outDir`.
10. In the sysHUB System database create a new table `usertotp`:
    ```sql
    CREATE TABLE [dbo].[usertotp](
        [uuid] [varchar](32) NOT NULL,
        [totpkey] [varchar](128) NOT NULL,
    CONSTRAINT [PK_usertotp] PRIMARY KEY CLUSTERED 
    (
        [uuid] ASC
    )WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON, OPTIMIZE_FOR_SEQUENTIAL_KEY = OFF) ON [PRIMARY]
    ) ON [PRIMARY]
    GO

    ALTER TABLE [dbo].[usertotp]  WITH CHECK ADD  CONSTRAINT [FK_usertotp_usertotp] FOREIGN KEY([uuid])
    REFERENCES [dbo].[usertotp] ([uuid])
    GO

    ALTER TABLE [dbo].[usertotp] CHECK CONSTRAINT [FK_usertotp_usertotp]
    GO
    ```

## Test and usage
1. Back in the webclient refresh the page in order to get the custom menu displayed.
2. In the top navigation bar next to Action and Run you'll find Menu Extension.
3. Open the Menu Extension dropdown and click on *2FA* to open the new dialog.
4. Press the red `Generate QR-Code` button. In case of any error you'll find more info in the server log. If you see the QR-code anything worked.
5. Scan the QR-code with an app like Microsoft Authenticator, Google Authenticator, Authy, etc.
6. Input the current 6-digit 2FA code into the dialog in the webclient and press the green `Validate code` button.

## Summary
You're done. Feel free to share your feedback or improvements.

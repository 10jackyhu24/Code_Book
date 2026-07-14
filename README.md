# Code Book

## Code Book Environment Setup Guide
### 1. Install Pandoc
Open Command Prompt (CMD) or PowerShell and run the following command to install Pandoc:
```
winget install --source winget --exact --id JohnMacFarlane.Pandoc
```

### 2. Install TinyTeX (Windows Portable Version)
1. Go to the [TinyTeX GitHub Releases](https://github.com/rstudio/tinytex-releases/releases) page and download the latest Windows archive (e.g., `TinyTeX-v2026.05.zip`).

2. Extract the downloaded `.zip` file, and copy the extracted `TinyTeX` folder into `C:\Users\<YourUsername>\AppData\Roaming\`.
    * Important Directory Structure: Ensure the final path is exactly `C:\Users\<YourUsername>\AppData\Roaming\TinyTeX`, and not nested like `...\Roaming\TinyTeX-v2026.05\TinyTeX`.

3. Navigate into the `C:\Users\<YourUsername>\AppData\Roaming\TinyTeX\bin\windows\` directory.

4. Locate the `tlmgr.bat` file, right-click it, and select **"Run as administrator"**. If the command window displays the following message, the script has executed successfully:
    ```
    No arguments given!
    Use tlshell as a GUI for tlmgr.
    Press any key to continue . . .
    ```

5. **Manually Add to Environment Variables:** Copy the core binary path `C:\Users\<YourUsername>\AppData\Roaming\TinyTeX\bin\windows` and add it to your User or System **Path** variable. This allows Windows to recognize the `tlmgr` and `xelatex` commands from any directory.

### 3. Compilation Command (Quick Reference)
Once the setup is complete, navigate to your markdown code_book folder and run this command to generate your 2-column A4 Code Book:

```
pandoc codebook.md -o codebook.pdf --pdf-engine=xelatex -V geometry:"a4paper, top=1.8cm, bottom=0cm, left=1cm, right=1cm" -V classoption=twocolumn -V fontsize=9pt --include-in-header=header.tex
```
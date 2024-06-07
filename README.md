# Streamlit to Executable
#### [Tutorial](https://youtu.be/G7Qeg_rbYM8)

## Create a Virtual Environment

```bash
pyenv virtualenv <version> .<env-name>
# or
python -m venv .<env-name>
# THE DOT IS IMPORTANT!
```

# Activate the Virtual Environment

```bash
pyenv activate <env-name>
# or
.<env-name>\\Scripts\\activate.bat
```

# Verify the Virtual Environment

```bash
python --version
```

# Deactivate the Virtual Environment

```bash
pyenv deactivate
# or
.<env-name>\\Scripts\\deactivate.bat
```

# Install Streamlit and Other Required Libraries

```bash
# You can use the latest version
pip install streamlit pyinstaller
```

# Add the Main File (app.py)

```bash
echo > app.py
```

# Create an Entry Point for the Executable (run_app.py)

```bash
echo > run_app.py
```

# Add Content to Your Files

- app.py:

```python
import streamlit as st

if __name__ == '__main__':
    st.header("Hello, World!")
```

- run_app.py

```python
from streamlit.web import cli

# This import path depends on your Streamlit version
if __name__ == '__main__':
    cli._main_run_clExplicit('app.py', args=['run'])
    # We will CREATE this function inside our Streamlit framework

```

# Navigate to the Streamlit Path

In the version we are using, it is located at: `.env\Lib\site-packages\streamlit\web\cli.py`

# Add the Magic Function
```python
# ... def main(log_level="info"):
# [...]
# You can use any name you prefer as long as it starts with an underscore
def _main_run_clExplicit(file, is_hello, args=[], flag_options={}):
    bootstrap.run(file, is_hello, args, flag_options)

# ...if __name__ == "__main__":
# ...    main()
```

# Create a Hook to Get Streamlit Metadata

- .\hooks\hook-streamlit.py
```python
from PyInstaller.utils.hooks import copy_metadata

datas = copy_metadata('streamlit')
```

# Compile the App
Run the following command to create the first run_app.spec file. 
Note that if you are using auto-py-to-exe, you can't edit spec files here; 
you should edit them from the interface in the advanced options.

```bash
pyinstaller --onefile --additional-hooks-dir=./hooks run_app.py --clean
# --onefile: Create a single output file
# --clean: Delete cache and remove temporary files before building
# --additional-hooks-dir: An additional path to search for hooks. This option can be used multiple times.
```

# Create Streamlit Configuration Files

You can add these files to your project's root and the output folder, or just the output folder.

- .streamlit\config.toml
```bash
[global]
developmentMode = false

[server]
port = 8502
```

# Copy the Configuration Files to the Output Folder
```bash
xcopy /s /e .streamlit output/.streamlit
# Select D = directory
```

# Copy app.py to the Output Folder
```bash
copy app.py output/app.py
```

# Add the Data to the New Hook in run_app.spec
```python
...
a = Analysis(
    ...
    datas=[
        (".env/Lib/site-packages/altair/vegalite/v5/schema/vega-lite-schema.json",
        "./altair/vegalite/v5/schema/"),
        (".env/Lib/site-packages/streamlit/static",
        "./streamlit/static"),
        (".env/Lib/site-packages/streamlit/runtime",
        "./streamlit/runtime"),
    ]
    ...
)
...

```
# Notes
```python
# 
# this path pair should be in that way
# but I believe it is because we add the tuple as this templete
# (absolut_path, parent_path)
# so for files that is in the root of `Lib/site-packages` 
# We can add only the dot as parent 
# i.e: (".envir/Lib/site-packages/wmi.py",".")
# for folders the behaviour is the same
```

# Build the Executable

```bash
pyinstaller run_app.spec --clean
```

## 🎈 It's done! run your run_app.exe file and see the magic 🪄

<pre>Huge Thanks To: hmasdev<pre>
<pre>I'm organizing the solution from <a href="https://discuss.streamlit.io/t/using-pyinstaller-or-similar-to-create-an-executable/902/18"> hmasdev in the Streamlit Forum</a></pre>

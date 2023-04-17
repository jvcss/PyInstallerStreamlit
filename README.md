# Streamlit to Executable
#### [tutorial](https://youtu.be/G7Qeg_rbYM8)
## Create a virtual environment

```bash
pyenv virtualenv <version> <env-name>
#or
python -m venv .<env-name>
```

## Activate the virtual environment
```bash
pyenv activate <env-name>
#or
.\op a #.venv\Scripts\activate.bat
```

## Verify that the virtual environment
```bash
python --version
```

## To deactivate the virtual environment
```bash
pyenv deactivate
#or
.\op d #.venv\Scripts\deactivate.bat
```
---
## Install Streamlit and [pyinstaller OR auto-py-to-exe] and any other lib you'll use
```bash
#I'm using streamlit==1.8.1 But in the 
#Tutorial I explain what could change for us.
#So You could use the latest
pip install streamlit pyinstaller
```
## Now we add the main file called here app.py
```bash
echo > app.py
```
## And we need to create an entry point for our executable
```bash
echo > run_app.py
```


## At this point you should be able to add content

### app.py:
```python
import streamlit as st
if __name__ == '__main__':
    st.header("Hello world")
```

### run_app.py
```python
from streamlit.web import cli 
#this uri depends based on version of your streamlit
if __name__ == '__main__':
    cli._main_run_clExplicit('main.py', 'streamlit run')
    #we will CREATE this function inside our streamlit framework
```
---
## Now we need to navigate into the path inside streamlit veins
## in the version we are using here it is 
### .envir\Lib\site-packages\streamlit\web\cli.py
## It's because we are using our virtual environment
### now add our magic function
```python
#... def main(log_level="info"):
# [...]
# you can you the name you prefer ofcourse
# as long as you use underscore at the beginning
def _main_run_clExplicit(file, command_line, args=[],flag_options={}):
    main._is_running_with_streamlit = True
    bootstrap.run(file, command_line, args,flag_options)

# ...if __name__ == "__main__":
#...    main()
```

## Now we need a hook to get streamlit metadata.
## From the root of the project add a new folder and
## create a file to be more organized because
## in this folder will be created __pycache__
## infos that we need to keep safe
### .\hooks\hook-streamlit.py
### the dash could indicate it will be read for pyinstaller as any other hook
#### ( I actually don't know exacly why it is as it is)
```python
from PyInstaller.utils.hooks import copy_metadata
datas = copy_metadata('streamlit')
```

## OK now we have all we need to compile the app
## just run this line to create our first run_app.spec file
#### (if you are using the auto-py-to-exe we can't edit spec files from here we should edit from the interface in the advance options)
```bash
pyinstaller --onefile --additional-hooks-dir=./hooks run_app.py --clean
#the onfile indicate we are create a output file join
# everthing in it's binary
#Some use case you actually need --ondir instead
#the --clean delete cache and remove temporary files before building.
#--additional-hooks-dir An additional path to search for hooks. This option can be used multiple times.
```

## Now we just have to create an folder and file with streamlit configs
## It can be add in your root project and the output folder
## or just the output folder.
### .streamlit\config.toml
```toml
[global]
developmentMode = false

[server]
port = 8502
```
## Assuming your have create this folder in the root project
## as an expert streamlit you are
## you can simply copy it to the output folder
```bash
xcopy /s /e .streamlit output/.streamlit
#select D = directory
```

## And perform the same for your app.py that contains streamlit logic
```bash
copy app.py output/app.py
```

## Everything in place you now have only one thing to add
## the DATAS to the new hook we created
### in the run_app.spec that was generated add the following...
```spec
...
a = Analysis(
    //...
    datas=[
        (".env/Lib/site-packages/altair/vegalite/v4/schema/vega-lite-schema.json",
        "./altair/vegalite/v4/schema/"),
        (".env/Lib/site-packages/streamlit/static",
        "./streamlit/static"),
        (".env/Lib/site-packages/streamlit/runtime",
        "./streamlit/runtime"),
    //...)
...
```
```bash
# 
# this path pair should be in that way
# but I believe it is because we add the tuple as this templete
# (absolut_path, parent_path)
# so for files that is in the root of `Lib/site-packages` 
# We can add only the dot as parent 
# i.e: (".envir/Lib/site-packages/wmi.py",".")
# for folders the behaviour is the same
```
# All the modifications in the datas should be loaded using the command
```bash
pyinstaller run_app.spec --clean
```



## 🎈 It's done! run your run_app.exe file and see the magic 🪄

<pre>Huge Thanks To: hmasdev<pre>
<pre>I'm organizing the solution from <a href="https://discuss.streamlit.io/t/using-pyinstaller-or-similar-to-create-an-executable/902/18"> hmasdev in the Streamlit Forum</a></pre>

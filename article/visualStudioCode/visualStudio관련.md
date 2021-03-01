
launch.jon
```yml
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Python",
            "type": "python",
            "stopOnEntry": false,
            "request": "launch",
            "program": "${file}",
            "cwd": "${workspaceRoot}",
            "env": {
                "PYTHONPATH": "/Users/parksanghyun/opt/spark_binary/spark_current/python/lib:/Users/parksanghyun/opt/aws-glue-libs/PyGlue.zip:/Users/parksanghyun/opt/aws-glue-libs/awsglue",
                "SPARK_CONF_DIR": "${GLUE_HOME}/conf"
            }
        }
    ]
}
```


settings.json
```yml
{
    "python.pythonPath": "/Users/sanghyun/.local/share/virtualenvs/tdd-qgx-_ySO/bin/python",
    "python.autoComplete.extraPaths": [],
    "python.testing.unittestArgs": [
        "-v",
        "-s",
        "./test",
        "-p",
        "*_test.py"
    ],
    "python.testing.pytestEnabled": false,
    "python.testing.nosetestsEnabled": false,
    "python.testing.unittestEnabled": true,
    "python.linting.flake8Enabled": true,
    "python.linting.enabled": true,
    "python.linting.flake8Args": [
        "--max-line-length=120",
        "--ignore=E402,F841,F401,F403,F405,E302,E305,E501,W291" // 해당 내용 관련 해서는 차후 더 확인이 필요 
    ],
    // "debug.internalConsoleOptions":"",
    "debug.console.wordWrap": false,
    "workbench.list.horizontalScrolling": true,
    "editor.rulers": [
        120
    ],
    "[python]": {
        "editor.codeActionsOnSave": {
            "source.organizeImports": true
        }
    },
    "python.formatting.provider": "black",
    "python.formatting.blackArgs": [
        "--line-length",
        "120"
    ],
    "editor.formatOnSave": true,
    "debug.console.closeOnEnd": true,
    "debug.console.fontSize": 11,
    "debug.inlineValues": true,
}
```
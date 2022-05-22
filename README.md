# blog

1. theme submodule 되어 있을테니 git clone
    ```
    $ cd themes
    $ rm -r LoveIt
    $ git clone https://github.com/dillonzq/LoveIt.git
    ```
 2. hugo로 build  
    `hugo`
 3. github.io submodule(submodule 등록되어있을테니 일단 진행)
    ```
    $ rm -rf public
    $ git clone {io} public
    $ git pull
    ```
 4. deploy shell 사용

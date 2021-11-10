# Домашнее задание к занятию «2.4. Инструменты Git»


### 1. Найдите полный хеш и комментарий коммита, хеш которого начинается на aefea.  

$ git show aefea --format="Hash=%H Comment=%s" -s  
Hash=aefead2207ef7e2aa5dc81a34aedf0cad4c32545 Comment=Update CHANGELOG.md  

### 2. Какому тегу соответствует коммит 85024d3?  

$ git show 85024d3  
или  
$ git tag --points-at 85024d3  
v0.12.23  

### 3. Сколько родителей у коммита b8d720? Напишите их хеши.  
$ git show --pretty="Commit: %h, Parents: %P" b8d720  
Commit: b8d720f83, Parents: 56cd7859e05c36c06b56d013b55a252d0bb7e158 9ea88f22fc6269854151c571162c5bcf958bee2b  

### 4. Перечислите хеши и комментарии всех коммитов которые были сделаны между тегами v0.12.23 и v0.12.24.
$ git log v0.12.23...v0.12.24 --oneline  
33ff1c03b (tag: v0.12.24) v0.12.24  
b14b74c49 [Website] vmc provider links  
3f235065b Update CHANGELOG.md  
6ae64e247 registry: Fix panic when server is unreachable  
5c619ca1b website: Remove links to the getting started guide's old location  
06275647e Update CHANGELOG.md  
d5f9411f5 command: Fix bug when using terraform login on Windows  
4b6d06cc5 Update CHANGELOG.md  
dd01a3507 Update CHANGELOG.md  
225466bc3 Cleanup after v0.12.23 release  

### 5. Найдите коммит в котором была создана функция func providerSource, ее определение в коде выглядит так func providerSource(...) (вместо троеточего перечислены аргументы).  
$ git log -S 'func providerSource' --oneline  
5af1e6234 main: Honor explicit provider_installation CLI config when present  
8c928e835 main: Consult local directories as potential mirrors of providers  

### 6. Найдите все коммиты в которых была изменена функция globalPluginDirs.  
$ git log -L :'func globalPluginDirs':plugins.go --oneline  
Коммиты:  
78b122055 Remove config.go and update things using its aliases  
52dbf9483 keep .terraform.d/plugins for discovery  
41ab0aef7 Add missing OS_ARCH dir to global plugin paths  
66ebff90c move some more plugin search path logic to command  
8364383c3 Push plugin discovery down into command package  

    $ git log -L :'func globalPluginDirs':plugins.go --oneline
    78b122055 Remove config.go and update things using its aliases

    diff --git a/plugins.go b/plugins.go
    --- a/plugins.go
    +++ b/plugins.go
    @@ -16,14 +18,14 @@
     func globalPluginDirs() []string {
            var ret []string
            // Look in ~/.terraform.d/plugins/ , or its equivalent on non-UNIX
	    dir, err := ConfigDir()
            dir, err := cliconfig.ConfigDir()
            if err != nil {
                    log.Printf("[ERROR] Error finding global config directory: %s", err)
            } else {
                    machineDir := fmt.Sprintf("%s_%s", runtime.GOOS, runtime.GOARCH)
                    ret = append(ret, filepath.Join(dir, "plugins"))
                    ret = append(ret, filepath.Join(dir, "plugins", machineDir))
            }

            return ret
     }
    52dbf9483 keep .terraform.d/plugins for discovery

    diff --git a/plugins.go b/plugins.go
    --- a/plugins.go
    +++ b/plugins.go
    @@ -16,13 +16,14 @@
     func globalPluginDirs() []string {
            var ret []string
            // Look in ~/.terraform.d/plugins/ , or its equivalent on non-UNIX
            dir, err := ConfigDir()
            if err != nil {
                    log.Printf("[ERROR] Error finding global config directory: %s", err)
            } else {
                    machineDir := fmt.Sprintf("%s_%s", runtime.GOOS, runtime.GOARCH)
                    ret = append(ret, filepath.Join(dir, "plugins"))
                    ret = append(ret, filepath.Join(dir, "plugins", machineDir))
            }

            return ret
     }
    41ab0aef7 Add missing OS_ARCH dir to global plugin paths

    diff --git a/plugins.go b/plugins.go
    --- a/plugins.go
    +++ b/plugins.go
    @@ -14,12 +16,13 @@
     func globalPluginDirs() []string {
            var ret []string
            // Look in ~/.terraform.d/plugins/ , or its equivalent on non-UNIX
            dir, err := ConfigDir()
            if err != nil {
                    log.Printf("[ERROR] Error finding global config directory: %s", err)
            } else {
                    ret = append(ret, filepath.Join(dir, "plugins"))
                    machineDir := fmt.Sprintf("%s_%s", runtime.GOOS, runtime.GOARCH)
                    ret = append(ret, filepath.Join(dir, "plugins", machineDir))
            }

            return ret
     }
    66ebff90c move some more plugin search path logic to command

    diff --git a/plugins.go b/plugins.go
    --- a/plugins.go
    +++ b/plugins.go
    @@ -16,22 +14,12 @@
     func globalPluginDirs() []string {
            var ret []string
     
            // Look in the same directory as the Terraform executable.
            // If found, this replaces what we found in the config path.
            exePath, err := osext.Executable()
            if err != nil {
                   log.Printf("[ERROR] Error discovering exe directory: %s", err)
            } else {
                    ret = append(ret, filepath.Dir(exePath))
            }
     
            // Look in ~/.terraform.d/plugins/ , or its equivalent on non-UNIX
            dir, err := ConfigDir()
            if err != nil {
                    log.Printf("[ERROR] Error finding global config directory: %s", err)
            } else {
                    ret = append(ret, filepath.Join(dir, "plugins"))
            }

            return ret
     }
    8364383c3 Push plugin discovery down into command package

    diff --git a/plugins.go b/plugins.go
    --- /dev/null
    +++ b/plugins.go
    @@ -0,0 +16,22 @@
    +func globalPluginDirs() []string {
            var ret []string
     
            // Look in the same directory as the Terraform executable.
            // If found, this replaces what we found in the config path.
            exePath, err := osext.Executable()
            if err != nil {
                    log.Printf("[ERROR] Error discovering exe directory: %s", err)
            } else {
                    ret = append(ret, filepath.Dir(exePath))
            }
     
            // Look in ~/.terraform.d/plugins/ , or its equivalent on non-UNIX
            dir, err := ConfigDir()
            if err != nil {
                    log.Printf("[ERROR] Error finding global config directory: %s", err)
            } else {
                    ret = append(ret, filepath.Join(dir, "plugins"))
            }
    
           return ret
     }

### 7. Кто автор функции synchronizedWriters?  
$ git log -S 'func synchronizedWriters' --pretty="Commit:%h, Author: %an"  
Commit:bdfea50cc, Author: James Bardin  
Commit:5ac311e2a, Author: Martin Atkins  

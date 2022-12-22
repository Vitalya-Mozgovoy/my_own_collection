# Домашнее задание к занятию "09.04 Jenkins"

<details>
<summary>Подготовка к выполнению</summary>

## Подготовка к выполнению

1. Создать 2 VM: для jenkins-master и jenkins-agent.
2. Установить jenkins при помощи playbook'a.
3. Запустить и проверить работоспособность.
4. Сделать первоначальную настройку.

</details>


## Основная часть

1. Сделать Freestyle Job, который будет запускать `molecule test` из любого вашего репозитория с ролью.
2. Сделать Declarative Pipeline Job, который будет запускать `molecule test` из любого вашего репозитория с ролью.
3. Перенести Declarative Pipeline в репозиторий в файл `Jenkinsfile`.
4. Создать Multibranch Pipeline на запуск `Jenkinsfile` из репозитория.
5. Создать Scripted Pipeline, наполнить его скриптом из [pipeline](./pipeline).
6. Внести необходимые изменения, чтобы Pipeline запускал `ansible-playbook` без флагов `--check --diff`, если не установлен параметр при запуске джобы (prod_run = True), по умолчанию параметр имеет значение False и запускает прогон с флагами `--check --diff`.
7. Проверить работоспособность, исправить ошибки, исправленный Pipeline вложить в репозиторий в файл `ScriptedJenkinsfile`.
8. Отправить две ссылки на репозитории в ответе: с ролью и Declarative Pipeline и c плейбукой и Scripted Pipeline.

**Answers**  

vector-role Jenkinsfile

----------------------------

pipeline {
 agent { label 'molecule' }
 environment {
        ROLE = "vector-role"
    }
 parameters {
 choice(name: 'TEST', choices: ['only default', 'all'], description: 'Test cases')
    }
 stages {
 stage('Git checkout') {
 steps {
 echo "Testing role  ${env.ROLE}"
 checkout([
 $class: 'GitSCM', 
                    branches: [[name: "*/${env.ROLE}"]], 
                    extensions: [], 
                    userRemoteConfigs: [[url: 'https://github.com/Vitalya-Mozgovoy/my_own_collection.git ']]
                ])
            }
        }
 stage("Run molecule") {
            steps {
 script {
                    if ('only default' != params.TEST) {
 sh "molecule test --all"
                    }
                    else {
 sh 'molecule test'
                    }
                }
            }
        }
    }
}

--------------------------------------------

lighthouse-role Jenkinsfile


pipeline {
 agent { label 'molecule' }
 environment {
        ROLE = "lighthouse-role"
    }
 parameters {
 choice(name: 'TEST', choices: ['only default', 'all'], description: 'Test cases')
    }
 stages {
 stage('Git checkout') {
 steps {
 echo "Testing role  $ {env.ROLE}"
 checkout([
 $class: 'GitSCM', 
                    branches: [[name: "*/${env.ROLE}"]], 
                    extensions: [], 
                    userRemoteConfigs: [[url: 'https://github.com/Kraktorist/devops-netology.git ']]
                ])
            }
        }
 stage("Run molecule") {
 steps {
 script {
                    if ('only default' != params.TEST) {
 sh 'molecule test --all'
                    }
                    else {
 sh 'molecule test'
                    }
                }
            }
        }
    }
}




[ScriptedJenkinsfile](assets/answers/ScriptedJenkinsfile)

---

[exported jobs](assets/answers/xml/)

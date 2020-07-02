pipeline{
    agent{
        label "test1"
    }
    parameters{
        booleanParam(defaultValue: true,description: '',name: 'userFlag')             //定义参数
        booleanParam(defaultValue: "master",description: '',name: 'groupFlag')        //默认值为true或false。若为字符串默认为true
        string(name: 'DEPLOY_ENV',defaultValue: 'staging',description: '')            //name可以任意写，但是需要手动启动一次流水线后才能有效
        text(name: 'DEPLOY_TEXT',defaultValue: 'One\nTwo\nThree\n',description: '')   //name可以任意写，但是需要手动启动一次流水线后才能有效
        choice(name: 'branch',choices: 'dev\ntest\nstaging\n',description: '选择构建环境')
        password(name: 'PASSWD',defaultValue: 'SECRET',description: '选择密码')
    }

    triggers{                            
    //    cron('*/50 * * * *')        //触发任务。每500分钟执行一次
    //    pollSCM('H/1 * * * *')         //每分钟查看一下代码库是否有变化
        //upstream(upstreamProjects:'job1,job2',threshold:hudson.model.Result.SUCCESS) //当job1和job2任务运行的时候触发本任务，hudson.model.Result是一个枚举：ABORTED/FAILURE/SUCCESS/UNSTABLE/NOT_BULT
        gitlab(triggerOnPush: true,                                //触动触发
               triggerOnMergeRequest: true,                        //merge触发
               branchFilterType: 'All',                            //选择分支，只有符合条件的分支才能被触发（必选）
               secretToken: "e18e152257c5cd9739f1892b6da55ea0"     //配置secretToken，由jenkins生成 
        )
    }
    
    options{
        buildDiscarder(logRotator(numToKeepStr:'10'))   //自动清理pipeline执行后生成的日志，并确定保留数量
        checkoutToSubdirectory('subdir')                //将拉取的源码指定存到默认根目录的子目录subdir下
        disableConcurrentBuilds()                       //禁止多个pipeline同时执行
        retry(2)                                        //发生失败时可以重试几次
     //   timeout(time:400,unit:'SECONDS')              //pipeline执行超过10分钟时，自动中止执行，也可以SECONDS\HOURS\MINUTES
    //    gitLabConnection('gitlab')
    }
    
    environment{                                        //pipeline的全局环境变量,变量名不区分大小写mm和MM为同名.通过env.mm可以查看环境变量值
        mm = "MRy"                                      //也可以通过jenkins配置pipeline之外的全局变量
    }
    
    //tools{                                            //可以自动下载并安装指定的工具
      //  maven 'mvn-3.5.4'
    //}
    
    stages{
        //stage("deploy input"){
        //    steps{
        //        input message: "发财或不用"
        //    }
        //}
        stage("foo"){
            steps{
                echo "flag:${params.userFlag}"
                echo "${params.groupFlag}" 
                echo "${DEPLOY_ENV}"
                echo "${DEPLOY_TEXT}"//输出参数
            }
        }
        stage("job"){
            steps{
                build(
                    job:"pipeline-hello-world_multibranch/private",
                    parameters:[
                        booleanParam(name: 'userFlag',value:true),
                        booleanParam(name: 'groupFlag',value: "master",description: ''),          //默认值为true或false。若为字符串默认为true
                        string(name: 'DEPLOY_ENV',value: 'staging',description: ''),              //name可以任意写，但是需要手动启动一次流水线后才能有效
                        text(name: 'DEPLOY_TEXT',value: 'One\nTwo\nThree\n',description: ''),     //name可以任意写，但是需要手动启动一次流水线后才能有效
                        //choice(name: 'branch',choices: 'dev\ntest\nstaging',description: '选择构建环境'),
                        password(name: 'PASSWD',value: 'SECRET',description: '选择密码')
                    ]
                )
            }
        }
        stage("deploy test1"){
            when{
                expression{return params.branch == "test"}
            }
            steps{
                echo "deploy to test"
            }
        }
        stage("deploy test2"){
            when{
                expression{return params.branch == "dev"}
            }
            steps{
                echo "deploy to dev"
            }
        }
        stage('build'){
            environment{                                                 //stage内的环境变量
                BULD_MM = "build_yml"
                M_M = "${mm}_jenkins"                                    //环境变量调用，若定义的环境变量在env内存在则覆盖
                JAVA_HOME = "/usr/lib/jvm/java-1.8.0/bin"
            }
            steps{
                script{
                    fileExists('/mnt/jenkins/workspace/pipeline-hello-world/subdir/pom-prod.xml')   //判断文件是否存在
                    sleep 5
                    //   timeout(time:400,unit:'SECONDS'){
                    //       input message: "发布或停止"
                    //}
                    pwd
                    dir("/mnt/jenkins/workspace/pipeline-hello-world/subdir"){                      //切换到/home/dirl目录
//                    deleteDir()                                                                   //删除当前目录即/home下的dirl目录
                }
                    echo "hello jim ${mm}"
                    sh "ls -l;pwd"
                    def result = []
                    def browsers = ['chrome','firefox','jim','harry']                                             //groovy代码
                    def t = tool name: 'docker', type: 'org.jenkinsci.plugins.docker.commons.tools.DockerTool'    //可通过Pipeline Syntax的tool：..模块生成
                    echo "${t}"                                                                                   //输出安装目录
                    for (i=0;i<browsers.size();i++){
                        echo "Testing the ${browsers[i]} browsers"
                }
                }
      //              sh "mvn clean test install"
    //                sh"printenv"                        //将env变量的属性值打印出来
                   echo "${BULD_MM} ${mm}  ${M_M}"
    //                sh "${JAVA_HOME}/java -version"
            }
            post{
                always{
                    echo "stage post always"
                    cleanWs()                             //清空当前目录内容
    //                //sh "yum -y reinstall git"
    //                //erro("emmm....")
                }
            }
    }
    
        stage('example'){
           // tools{
             //   jdk "jdk9.0.4"
            //}
            steps{
                echo "build world"
                echo "Running ${env.mm} on ${env.mm}"                          //输出构建的流水线数量及构建的url
                echo "Running $env.BUILD_NUMBER on $env.JENKINS_URL"           //方法二
                echo "Running $env.artifactApiUrl on $env.JENKINS_URL"
                sh "printenv"                                                  //将env变量的属性值打印出来
            }
        }
        stage('deploy to test'){
            when{
                branch 'private2';
                environment name:"mm",value: ":Ry"                             //上述两个条件需要同时满足才能执行下一步。
            }
            steps{
                echo 'private'
            }
        }
        stage('deploy to test3'){
            when{
                branch 'private2'
            }
            steps{
                echo 'private2'
            }
        }
        stage('deploy to pod'){
            when{
                branch 'private1'
            }
            steps{
                echo "private1"
            }
        }
}

    post{
        changed{
          echo "pipeline post changed"
    }
        always{
          echo "pipeline post always"
    }
        success{
          echo "pipeline post success"
    }
        failure{
          echo "pipeline post failure"
   }
}
    
}

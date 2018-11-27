---
layout: post
title:  Enviroment Settings -- Tools
date:   2018-11-24 22:51:00 +0800
categories: 环境设置
---

> 


## 1. Shell Tools

### Ssh 登录
Ssh命令的不便在于，参数 `PreferredAuthentications` 过长；连接目标的名称包含数字，抽象且多变。这两点都不便于记忆。现将ssh命令分为动作 `jump` 和目标 `foo-bar` 两部分，组合使用。

+ jump
{% highlight shell %}
#!/bin/bash
ssh -o PreferredAuthentications=password "$1" "$2"
{% endhighlight %}

+ foo-bar
{% highlight shell %}
foo-bar() {
  echo 'foo@111.222.333.444'
}
{% endhighlight %}

**使用示例**
{% highlight shell %}
jump $(foo-bar)
{% endhighlight %}

## 2. PHP Tools
点击参考[源码](https://github.com/zhang1career/mytools)

### 工具准备
使用 `Symfony/Component/Console/Application` 搭建 PHP 命令行工具 `mit`，定义具体功能的基类 `Tool`。

+ mit
{% highlight php %}
$app = new Application($config['info']['app'], $config['info']['version']);
$app->add(new SomeTool());
try {
    $app->run();
} catch (Exception $e)
{
    printf($e->getMessage());
    return;
}
{% endhighlight %}

+ Tool
{% highlight php %}
class Tools extends Command
{
    public function execute(InputInterface $input, OutputInterface $output)
    {
        // get argument values
        $this->args = $input->getArguments();
        // get action
        $action = $this->namepath . '\\' . UCFirst(strtolower($this->getArgument('action')));
        /** @var CommandInterface $obj */
        $obj = new $action();
        $value = $obj->run($this->args);
        $output->writeln("<info>$value</info>");
        return 0;
    }
}
{% endhighlight %}

### LNMP 管理
定义 LNMP 管理的子类 `lnmp`，以及具体动作 `start`。

+ lnmp
{% highlight php %}
class Lnmp extends Tools
{
    static $NAME = 'lnmp';
    static $DESCRIPTION = 'Start LNMP services.';
    static $ARGUMENTS = [
        [
            'name' => 'action',
            'mode' => InputArgument::REQUIRED,
            'description' => 'Action the lnmp does, i.e. start, stop, restart',
        ],
    ];

    public function __construct($name = null)
    {
        parent::__construct($name);
        $this->namepath = __NAMESPACE__;
    }
}
{% endhighlight %}

+ start
{% highlight php %}
class Start implements CommandInterface
{
    public function run(array $args = [])
    {
        // create logs folder
        try {
            $this->mkdir('/tmp/logs/nginx');
            $this->mkdir('/tmp/logs/php');
            $this->mkdir('/tmp/logs/redis');
            $this->mkdir('/tmp/logs/risk-engine');
        } catch (Exception $e) {
            return $e;
        }

        // start nginx
        exec("pgrep nginx", $output, $err);
        if ($err) {
            exec('sudo nginx', $output, $err);
        } else {
            exec('sudo nginx -s reload', $output, $err);
        }

        // start php-fpm
        exec('launchctl unload -w /path/to/php.plist', $output, $err);
        exec('launchctl load   -w /path/to/php.plist', $output, $err);

        return $err;
    }
}
{% endhighlight %}

**使用示例**：
{% highlight shell %}
mit lnmp start
{% endhighlight %}


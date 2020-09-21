# PHP防火墙

一个基于php的防火墙程序，拦截sql注入和xss攻击等

## 安装

``` bash
composer require xielei/waf
```

## 使用说明

``` php
$waf = new \Xielei\Waf();
$waf->run();
```

## 自定义拦截规则

``` php
$rules = [
    '\.\./', //禁用包含 ../ 的参数
    '\<\?', //禁止php脚本出现
    '\s*or\s+.*=.*', //匹配' or 1=1 ,防止sql注入
    'select([\s\S]*?)(from|limit)', //防止sql注入
    '(?:(union([\s\S]*?)select))', //防止sql注入
    'having|updatexml|extractvalue', //防止sql注入
    'sleep\((\s*)(\d*)(\s*)\)', //防止sql盲注
    'benchmark\((.*)\,(.*)\)', //防止sql盲注
    'base64_decode\(', //防止sql变种注入
    '(?:from\W+information_schema\W)', //防止sql注入
    '(?:(?:current_)user|database|schema|connection_id)\s*\(', //防止sql注入
    '(?:etc\/\W*passwd)', //防止窥探linux用户信息
    'into(\s+)+(?:dump|out)file\s*', //禁用mysql导出函数
    'group\s+by.+\(', //防止sql注入
    '(?:define|eval|file_get_contents|include|require|require_once|shell_exec|phpinfo|system|passthru|preg_\w+|execute|echo|print|print_r|var_dump|(fp)open|alert|showmodaldialog)\(', //禁用webshell相关某些函数
    '(gopher|doc|php|glob|file|phar|zlib|ftp|ldap|dict|ogg|data)\:\/', //防止一些协议攻击
    '\$_(GET|post|cookie|files|session|env|phplib|GLOBALS|SERVER)\[', //禁用一些内置变量,建议自行修改
    '\<(iframe|script|body|img|layer|div|meta|style|base|object|input)', //防止xss标签植入
    '(onmouseover|onerror|onload|onclick)\=', //防止xss事件植入
    '\|\|.*(?:ls|pwd|whoami|ll|ifconfog|ipconfig|&&|chmod|cd|mkdir|rmdir|cp|mv)', //防止执行shell
    '\s*and\s+.*=.*' //匹配 and 1=1
];

$waf = new \Xielei\Waf($rules);
$waf->run();
```

## 自定义拦截页面

``` php
$waf = new \Xielei\Waf();
if(!$waf->check()){
    echo '非法请求';
    die;
}
```

Интеграция ReCaptcha в 1С-Битрикс. Несколько ReCaptcha на одной странице.
Многие сталкивались с тем, что стандартная Captcha от Битрикс не справляется со спам ботами, как не настраивай ее. Даже, сделав ее практически не читаемой людьми, боты все-равно ее обходят.

Выход только в том, чтобы использовать капчи более продвинутые, логические и т.п., которых нет в Битриксе.

Очень удобный и довольно простой вариант - это ReCaptcha от Google. Помимо всего, у нее выходят новые версии, если вдруг ее начнут обходить какие-либо боты, но пока она неуязвима.
Интеграция ReCaptcha в 1С-Битрикс. Несколько ReCaptcha на одной странице

Установить ее "простым смертным" будет не просто, но если вы умеете создавать инфоблоки новостей в 1С-Битрикс, то осилите и интеграцию ReCaptcha в Битрикс.

Делаем все по шагам:

1. Заходим в личный кабинет ReCaptcha в Google https://www.google.com/recaptcha/admin#list, для этого у вас должен быть аккаунт в гугл, т.е. заведенная почта. И в этом личном кабинете добавляем сайт, на который встраиваем ReCaptcha. После добавления сайта вы получите 2-а ключа, которые нам нужны дальше.

2. Скачиваем библиотеку ReCaptcha тут https://github.com/google/recaptcha, далее из этой библиотеки берем только файлы из директории /SRC/ autoload.php и директорию /ReCaptcha/ и кjпируем их в /bitrix/php_interface/include/ (директорию /include/ нужно создать, ее там изначально нет).

3. Далее в init,php подключаем капчу с ключами, вставляя следующий код в самом начале файла, ну после <? :

@require_once 'include/autoload.php';
define("RE_SITE_KEY","6Lfi5R8TAAAAACpUg7U2IJjuTVMGglUsz-fgTgdu");
define("RE_SEC_KEY","6Lfi5R8TAAAAADVYQPeYcECtyFAQATCoIkruBdhrbn"); 
Ключи заменяем соответственно на свои, которые получили в личном кабинете.

4. Далее в шаблоне сайта или компонента подключаем js с Google:

<script src="https://www.google.com/recaptcha/api.js" async defer></script>

5. В шаблоне компонента (template.php), в месте отображения капчи вставляем код:

<div class="g-recaptcha" data-sitekey="<?=RE_SITE_KEY?>"></div>

6. В компоненте добавляем проверку, т.е. в файл /bitrix/components/bitrix/компонент/component.php (но только, лучше создать свое пространство имен, чтобы обновлениями не затереть все)

$recaptcha = new \ReCaptcha\ReCaptcha(RE_SEC_KEY);
$resp = $recaptcha->verify($_REQUEST['g-recaptcha-response'], $_SERVER['REMOTE_ADDR']);

if (!$resp->isSuccess()){
foreach ($resp->getErrorCodes() as $code) {
echo "Ошибка! Проверка не пройдена.";
echo $code;
return;
}
}
Теперь капча должна работать.
Как подключить несколько ReCaptcha на одной странице.
Кто пробовал уже поняли, что на странице будет отображаться только одна капча, а если у нас, например, 2-е и более форм на одной странице?! Решение тоже есть.

1, 2, 3 и 6-й шаги делаем, как описано выше.

4. Далее в КОНЦЕ шаблона сайта или компонента подключаем js с Google:

<script src="https://www.google.com/recaptcha/api.js?onload=onloadCallback&render=explicit" async defer></script>

5. В шаблоне компонента (template.php), в месте отображения капчи вставляем код:

<!--Первая форма...
  <div id="example1"></div>

<!--Вторая форма...
  <div id="example2"></div>

<!--Третья форма...
  <div id="example3"></div>

6. Все как описано выше.

7. Вставляем javascript инициализации, у Google в примере он находится в head, но мы размещали и после всех форм, т.к. нужно было генерировать после цикла foreach и все тоже работало. Код следующий:

<script type="text/javascript">
var verifyCallback = function(response) {
alert(response);
};
var widgetId1;
var widgetId2;
var onloadCallback = function() {
    widgetId1 = grecaptcha.render('example1', {
    'sitekey' : '<?=RE_SITE_KEY?>',
    });
    widgetId2 = grecaptcha.render(document.getElementById('example2'), {
    'sitekey' : '<?=RE_SITE_KEY?>'
    });
    grecaptcha.render('example3', {
    'sitekey' : '<?=RE_SITE_KEY?>',
    });
};
</script>

скопировано из https://devfix.ru/web_studio/blogs/691/

для корректной работы по ssh необходимо что бы были включены следующие параметры php
Чтобы разрешить упаковку https:

php_opensslрасширение должно существовать и быть включено
allow_url_fopen должен быть установлен в on
В файле php.ini вы должны добавить эти строки, если они не существуют:

extension=php_openssl.dll

allow_url_fopen = On

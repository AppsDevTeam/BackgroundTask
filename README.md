 BackgroundTask
===========

Run time-consuming tasks on background after browser gets it response from server.

Installation
------------

````
composer require adt/backgroundTask
````

Enable extension in your neon file:
````
extensions:
    backgroundTask: \ADT\BackgroundTask\DI\BackgroundTaskExtension
````

Run [Doctrine migrations](https://github.com/Zenify/DoctrineMigrations):
```
php www/index.php migrations:migrate
```

Register new Unix [Cron](https://en.wikipedia.org/wiki/Cron) in your crontab:
````
*/1 * * * * php /var/www/myapp/www/index.php adt:background-task > /dev/null
````

Configuration
---------

Neon example (with default values):
```
backgroundTask:
    threads: 16
```

Create background task
----------

Background task extends [Kdyby/Console](https://github.com/Kdyby/Console) Command to support functions for thread manipulation.

```php
<?php

namespace App\Model\BackgroundTask;

use \Adt\BackgroundTask\Task

class MyBackgroundTask extends Task {

    // TODO: ukázat použití funkcí, že jedno vlákno čeká až se dokončí nějaká část druhého a podobně

}
```

Plan your background task and show its status
------

Statuses:
- `STATUS_REQUEST` - task is planned
- `STATUS_START` - task is running
- `STATUS_DONE` - task is successfully done
- `STATUS_INTERRUPTED`- task has been interrupted before its end

TODO: přidat příklady, proč mohl být přerušen

```php
<?php

namespace App\Presenters;

use App\Model\BackgroundTask\MyBackgroundTask;

class MyAppPresenter extends BasePresenter
{
	/** @var \Adt\BackgroundTask\Service @autowire */
	protected $backgroundTaskService;
	
	public function actionDefault() {
	    $data = [
	        'idsToProcess' => [1, 2],   // get these from database or form
	    ];
	    
	    // Plan your background task
	    $this->backgroundTaskService->request(MyBackgroundTask::class, $data);
	}
	
	public function renderDefault() {
	    // Show background tasks status
	    $this->template->status = $this->backgroundTaskService->getStatus(MyBackgroundTask::class);
	}
	
}

```

TODO
---------

- Přidá mezi migrace novou migraci na přidání Cron tabulky
- Podle tagu zaregistruje servisy - něco jako command příkazy
- V command příkazech bude podpora pro práci s ostatními vlákny - čekání na dokončení jiného vlákna a podobně
- Cron servisa bude použitelná v kódu a přes ni se budou plánovat nové tasky a checkovat jejich aktuální stav
- Zadaní jobu je jméno a data, která daný task dostane. Možná pak ještě datum, pokud bychom to chtěli odložit.
- Asi budeme potřebovat nějak identifikovat různé instance tasků, ne? Nebo se nebude dát spustit více tasků stejné třídy? To by pak nedávalo smysl tam předávat nějaká data ke zpracování.


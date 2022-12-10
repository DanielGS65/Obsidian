## Fork

```ad-code
``` php
public function test(){
	$pid = pcntl_fork();

	if($pid == 0){
		Log::debug("Child");
	}
	else{
		Log::debug("Parent");
	}

	Log::debug("Finish");

	return 0;
}
```

## Wait


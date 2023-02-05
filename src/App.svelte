<script>
	import Tesseract from 'tesseract.js';
	import { global } from 'svelte/internal';
	
	var filename;
	var image = "";
	var response = "";
	let defaultSpeed = 1; 	
	var language = "fr";
	var pauseOuDemarre = "stop";
	var isLoading = false; 
	var currentFont = 30; 
	var numPage = 0;

	function changeSpeed(speed) {
		window.speechSynthesis.cancel();
		defaultSpeed = speed;
		console.log(response); 
		textToVoice(response); 
	}


	function upFont() {
		currentFont += 5; 
	}
	
	function downFont() {
		if (currentFont >= 6) {
			currentFont -= 5; 
		}
	}
	

	function changeLang() {
		window.speechSynthesis.cancel();
		if (language == "fr") {
			language = "en";
		} 
		else {
			language = "fr";
		}
	}

	function pauseStart() {
		console.log(pauseOuDemarre);
		if (pauseOuDemarre == "stop") {
			window.speechSynthesis.pause();
			pauseOuDemarre = "start";
		}
		else {
			window.speechSynthesis.resume(); 
			pauseOuDemarre = "stop";
		}
	}

	function textToVoice(message) {
	if ('speechSynthesis' in window) {
		var msg = new SpeechSynthesisUtterance();
		var voices = window.speechSynthesis.getVoices();
		msg.lang = language; //fr en-US
		msg.voice = voices[10]; //4 fille 3 gars
		msg.volume = 1; // de 0 to 1
		msg.rate = defaultSpeed; // de 0.1 to 10
		msg.pitch = 1; // de 0 to 2
		msg.text = message;
		window.speechSynthesis.speak(msg);
	}

	else{console.log(' Speech Synthesis Not Supported'); }
	}

			let opencambutton = document.querySelector("#opencamera");
			let video = document.querySelector("#video");
			let takepicbutton = document.querySelector("#takepic");
			let canvas = document.querySelector("#canvas");
			
			opencambutton.addEventListener('click', async function() {
				let stream = await navigator.mediaDevices.getUserMedia( {video: { 
					width: 1280 , 
					height: 720 }});
				video.srcObject = stream;
			});
			takepicbutton.addEventListener('click', function() {
				canvas.getContext('2d').drawImage(video, 0, 0, canvas.width, canvas.height);
					image = canvas.toDataURL('image/jpeg');
				
				console.log(image)

			});	

	/*
	 * Function to detect text in the image 
	*/
	function logTesseract() {
		numPage = 1; 
		isLoading = true; 
		Tesseract.recognize(
			image,
			'eng',
			{logger: m => console.log(m)}
		).then(({data: { text }}) => {
			response = text; 
			isLoading = false; 
			//################################################################# ADD FILTER RESPONSE
			textToVoice(response); 
		})
	}

	function goBack() {
		window.speechSynthesis.cancel();
		response = "";
		numPage--; 
	}


	if (annyang) {
        let msg = new SpeechSynthesisUtterance();
      // Let's define a command.
      const commands = {
    'start': () => { 
        //code = "";
        logTesseract();
        },
    'pause':() =>{
        window.speechSynthesis.pause();
        //code = "";
    },
    'resume':() =>{
        window.speechSynthesis.resume();
        //code = "";
    },
    'stop':()=>{
        window.speechSynthesis.cancel();
    },
    "command english":() =>{

        //let msgEn = new SpeechSynthesisUtterance();
        //var voices = window.speechSynthesis.getVoices();
        msg.lang = 'en';
        console.log("English");
        //msg.voice = voices[10]; //4 fille 3 gars
        msg.volume = 1; // de 0 to 1
        msg.rate = defaultSpeed; // de 0.1 to 10
        msg.pitch = 1; // de 0 to 2
        msg.text = "Here is the list of the commands: Say start to start the convertion. Say pause to pause the current lecture of the document. Say resume to continue the lecture. Say stop to cancel the lecture.";
        window.speechSynthesis.speak(msg);
    },
    "command french":()=>{
        //var msg = new SpeechSynthesisUtterance();
        //var voices = window.speechSynthesis.getVoices();
        msg.lang = 'fr';
        //msg.voice = voices[10]; //4 fille 3 gars
        msg.volume = 1; // de 0 to 1
        msg.rate = defaultSpeed; // de 0.1 to 10
        msg.pitch = 1; // de 0 to 2
        msg.text = "Voici la liste des commandes: start pour démarrer la la convertion. Dite pause pour pauser la lecture. Dite resioumme pour continuer la lecture. Dite stop pour arrêter la lecture.";
        window.speechSynthesis.speak(msg);
    },
    "switch language":()=>{
		changeLang();
    },
	"picture":()=>{
		console.log('here');
		document.querySelector('#takepic').click();
	}
};

      // Add our commands to annyang
      annyang.addCommands(commands);

  // Start listening.
      annyang.start();
        }
</script>

<main>
	
	{#if numPage == 0}
	<p>Your text is: </p>
	<p style="font-size:{currentFont}px;">{response}</p>
	<button on:click={logTesseract}>Read Image!</button>

	{:else}

	<p>Your text is: </p>
	<h2>
		{#if isLoading == true}
		Loading...
		{/if}
	</h2>
	<p style="font-size:{currentFont}px;">{response}</p>
	<button on:click={upFont}>+</button>
	<button on:click={downFont}>-</button>
	<button on:click={pauseStart}>Pause/Start</button>
	<button on:click={() => changeSpeed(5)}>Read Faster</button>
	<button on:click={() => changeSpeed(1)}>Normal speed</button>
	<button on:click={logTesseract}>Read Image!</button>
	<button on:click={changeLang}>
		{#if language == "fr"}
		French
		{:else}
		English
		{/if}
	</button>
	<button on:click={goBack}>Go Back</button>
	{/if}
</main>

<style>
	main {
		text-align: center;
		padding: 1em;
		max-width: 240px;
		margin: 0 auto;
	}

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}

</style>

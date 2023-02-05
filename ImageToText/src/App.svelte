<script>
	import Tesseract from 'tesseract.js';
	import { global } from 'svelte/internal';
	
	var image;
	var response = "";
	let defaultSpeed = 1; 	

	function fasterSpeech() {
		defaultSpeed = 5;
		textToVoice(response); 
	}
	
	function normalSpeech() {
		defaultSpeed = 1;
		textToVoice(response);
	}

	function textToVoice(message) {
	if ('speechSynthesis' in window) {
		var msg = new SpeechSynthesisUtterance();
		var voices = window.speechSynthesis.getVoices();
		msg.lang = 'fr';
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
		Tesseract.recognize(
			image,
			'eng',
			{logger: m => console.log(m)}
		).then(({data: { text }}) => {
			response = text; 
			textToVoice(response); 
		})
	}

</script>

<main>
	<button on:click={fasterSpeech}>Read Faster</button>
	<button on:click={normalSpeech}>Normal speed</button>
	<button on:click={logTesseract}>Read Image!</button>
	<p>Your text is: </p>
	<p>{response}</p>
</main>

<style>
	main {
		text-align: center;
		padding: 1em;
		max-width: 240px;
		margin: 0 auto;
	}

	h1 {
		color: #ff3e00;
		text-transform: uppercase;
		font-size: 4em;
		font-weight: 100;
	}

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
</style>

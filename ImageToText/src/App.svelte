<script>
	import Tesseract from 'tesseract.js'

	let response = "";

	function textToVoice(message) {
	if ('speechSynthesis' in window) {
		var msg = new SpeechSynthesisUtterance();
		var voices = window.speechSynthesis.getVoices();
		msg.lang = 'en';
		msg.voice = voices[4]; //4 fille 3 gars
		msg.volume = 1; // de 0 to 1
		msg.rate = 1; // de 0.1 to 10
		msg.pitch = 1; // de 0 to 2
		msg.text = message;
		window.speechSynthesis.speak(msg);
	}

	else{console.log(' Speech Synthesis Not Supported'); }
	}


	function logTesseract() {
		Tesseract.recognize(
			'https://tesseract.projectnaptha.com/img/eng_bw.png',
			'eng',
			{ logger: m => console.log(m)}
		).then(({data: { text }}) => {
			response = text; 
			textToVoice(response); 
		})
	}

</script>

<main>
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

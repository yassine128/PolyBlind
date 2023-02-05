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
	<input type="hidden" id="anPageName" name="page" value="macbook-pro-14-1" />
    <div class="container-center-horizontal">
      <div class="macbook-pro-14-1 screen">
        <div class="overlap-group5">
          <div class="overlap-group8">
            <div class="button_capture"><div class="capture inter-bold-white-24px">Capture</div></div>
            <div class="button_open-cam"><div class="open-cam inter-bold-white-24px">Open Cam</div></div>
            <div class="overlap-group6">
              <div class="bubble3"></div>
              <div class="logo">
                <div class="overlap-group4">
                  <div class="paper_fill"><img class="subtract" src="img/subtract@2x.png" alt="Subtract" /></div>
                  <h1 class="title">Reader</h1>
                </div>
              </div>
            </div>
          </div>
          <div class="flex-row">
            <div class="bubble2"></div>
            <div class="read_button"><div on:click={logTesseract} class="read inter-bold-white-24px">Read!</div></div>
          </div>
          <div class="flex-row-1">
            <div class="button_-container">
              <div class="button">
                <div class="overlap-group">
                  <div class="x15 inter-bold-ocean-green-24px">x1.5</div>
                  <div class="rectangle-403"></div>
                  <div on:click={changeLang} class="fr inter-bold-ocean-green-22px">
					{#if language == "fr"}
					Fr
					{:else}
					En
					{/if}
				</div>
                </div>
              </div>
              <div class="button-1 button-3">
                <img on:click={pauseStart} class="_fill" src="img/stop-and-play-fill@2x.png" alt="Stop_and_play_fill" />
              </div>
              <div class="button_x">
                <div class="overlap-group">
                  <div class="x15-4 inter-bold-ocean-green-24px">x1.5</div>
                  <div class="rectangle-403"></div>
                  <div on:click={() => changeSpeed(1)} class="x1 inter-bold-ocean-green-22px">x1</div>
                </div>
              </div>
              <div on:click={upFont} class="button-2 button-3"><div class="text">+</div></div>
            </div>
            <div class="button_-container-1">
              <div class="button_x">
                <div class="overlap-group">
                  <div class="x15-4 inter-bold-ocean-green-24px">x1.5</div>
                  <div class="rectangle-403"></div>
                  <div on:click={() => changeSpeed(5)} class="x2 inter-bold-ocean-green-22px">x2</div>
                </div>
              </div>
              <div on:click={downFont} class="button-2 button-3"><div class="text">-</div></div>
            </div>
            <div class="overlap-group7"><div class="your-text">
				<p style="font-size: {currentFont}px;">{response}</p>
				{#if isLoading == true}
				Loading...
				{/if}
			</div></div>
          </div>
        </div>
      </div>
    </div>
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

	@import url("https://cdnjs.cloudflare.com/ajax/libs/meyer-reset/2.0/reset.min.css");
/* The following line is used to measure usage of this code in production. For more info see our usage billing page */
@import url("https://px.animaapp.com/63df1a9c241dcc1f34c43355.63df1a9cadcdfbdea71f54e9.xvombGA.hcp.png");


.container-center-horizontal {
  display: flex;
  flex-direction: row;
  justify-content: center;
  pointer-events: none;
  width: 100%;
}

.container-center-horizontal > * {
  flex-shrink: 0;
  pointer-events: auto;
}

@import url("https://cdnjs.cloudflare.com/ajax/libs/meyer-reset/2.0/reset.min.css");
@import url("https://fonts.googleapis.com/css?family=Inter:700|Italiana:400");
/* The following line is used to measure usage of this code in production. For more info see our usage billing page */
@import url("https://px.animaapp.com/63df1a9c241dcc1f34c43355.63df1a9cadcdfbdea71f54e9.un8gqea.hcp.png");



.container-center-horizontal {
  display: flex;
  flex-direction: row;
  justify-content: center;
  pointer-events: none;
  width: 100%;
}

.container-center-horizontal > * {
  flex-shrink: 0;
  pointer-events: auto;
}

* {
  box-sizing: border-box;
}




/* screen - macbook-pro-14-1 */

.macbook-pro-14-1 {
  align-items: flex-start;
  background-color: #00000033;
  border: 1px none;
  display: flex;
  height: 2946px;
  width: 1512px;
}

.macbook-pro-14-1 .overlap-group5 {
  align-items: flex-end;
  background-color: var(--ocean-green);
  display: flex;
  flex-direction: column;
  gap: 123px;
  min-height: 2946px;
  width: 1512px;
}

.macbook-pro-14-1 .overlap-group8 {
  height: 900px;
  position: relative;
  width: 1491px;
}

.macbook-pro-14-1 .button_capture {
  align-items: flex-start;
  background-color: var(--tuna);
  border-radius: 40px;
  display: flex;
  height: 52px;
  left: 81px;
  min-width: 151px;
  padding: 11px 8px;
  position: absolute;
  top: 472px;
}

.macbook-pro-14-1 .capture {
  letter-spacing: 0;
  line-height: normal;
  min-height: 29px;
  text-align: center;
  width: 135px;
}

.macbook-pro-14-1 .button_open-cam {
  align-items: flex-start;
  background-color: var(--tuna);
  border-radius: 40px;
  display: flex;
  height: 52px;
  left: 81px;
  min-width: 151px;
  padding: 11px 8px;
  position: absolute;
  top: 369px;
}

.macbook-pro-14-1 .open-cam {
  letter-spacing: 0;
  line-height: normal;
  min-height: 29px;
  text-align: center;
  width: 135px;
}

.macbook-pro-14-1 .overlap-group6 {
  height: 900px;
  left: 0;
  position: absolute;
  top: 0;
  width: 1491px;
}

.macbook-pro-14-1 .bubble3 {
  background-color: var(--gallery);
  border-radius: 102px 0px 0px 102px;
  height: 900px;
  left: 320px;
  position: absolute;
  top: 0;
  width: 1171px;
}

.macbook-pro-14-1 .logo {
  align-items: flex-start;
  display: flex;
  height: 78px;
  left: 0;
  min-width: 331px;
  position: absolute;
  top: 45px;
}

.macbook-pro-14-1 .overlap-group4 {
  height: 79px;
  margin-top: -1px;
  position: relative;
  width: 331px;
}

.macbook-pro-14-1 .paper_fill {
  align-items: center;
  display: flex;
  height: 78px;
  left: 17px;
  min-width: 60px;
  padding: 0 5.0px;
  position: absolute;
  top: 1px;
}

.macbook-pro-14-1 .subtract {
  height: 58px;
  width: 50px;
}

.macbook-pro-14-1 .title {
  color: var(--white);
  font-family: var(--font-family-italiana);
  font-size: var(--font-size-xl);
  font-weight: 400;
  left: 0;
  letter-spacing: 0;
  line-height: normal;
  position: absolute;
  text-align: center;
  top: 0;
  width: 331px;
}

.macbook-pro-14-1 .flex-row {
  align-items: center;
  align-self: flex-start;
  display: flex;
  gap: 93px;
  min-width: 1415px;
}

.macbook-pro-14-1 .bubble2 {
  background-color: var(--gallery);
  border-radius: 0px 102px 102px 0px;
  height: 900px;
  width: 1171px;
}

.macbook-pro-14-1 .read_button {
  align-items: flex-start;
  background-color: var(--tuna);
  border-radius: 40px;
  display: flex;
  height: 52px;
  min-width: 151px;
  padding: 9.3px 31.4px;
}

.macbook-pro-14-1 .read {
  letter-spacing: 0;
  line-height: normal;
  min-height: 31px;
  text-align: center;
  width: 88px;
}

.macbook-pro-14-1 .flex-row-1 {
  align-items: center;
  display: flex;
  min-width: 1421px;
}

.macbook-pro-14-1 .button_-container {
  align-items: flex-start;
  display: flex;
  flex-direction: column;
  gap: 38px;
  margin-bottom: 76.0px;
  min-height: 318px;
  width: 52px;
}

.macbook-pro-14-1 .button {
  align-items: flex-start;
  background-color: var(--tuna);
  border-radius: 18px;
  display: flex;
  margin-left: 1px;
  min-width: 51px;
}

.macbook-pro-14-1 .overlap-group {
  border-radius: 18px;
  height: 51px;
  position: relative;
  width: 51px;
}

.macbook-pro-14-1 .x15 {
  left: 0;
  letter-spacing: 0;
  line-height: normal;
  position: absolute;
  text-align: center;
  top: 9px;
  width: 51px;
}

.macbook-pro-14-1 .rectangle-403 {
  background-color: var(--tuna);
  border-radius: 18px;
  height: 51px;
  left: 0;
  position: absolute;
  top: 0;
  width: 51px;
}

.macbook-pro-14-1 .fr {
  left: 0;
  letter-spacing: 0;
  line-height: normal;
  position: absolute;
  text-align: center;
  top: 9px;
  width: 51px;
}

.macbook-pro-14-1 .button-1 {
  align-items: center;
  margin-left: 1px;
  padding: 0 9px;
}

.macbook-pro-14-1 ._fill {
  height: 33px;
  width: 33px;
}

.macbook-pro-14-1 .button_x {
  align-items: flex-start;
  background-color: var(--tuna);
  border-radius: 18px;
  display: flex;
  min-width: 51px;
}

.macbook-pro-14-1 .x1 {
  left: 0;
  letter-spacing: 0;
  line-height: normal;
  position: absolute;
  text-align: center;
  top: 9px;
  width: 51px;
}

.macbook-pro-14-1 .button-2 {
  align-items: flex-start;
  justify-content: flex-end;
  padding: 4px 8px;
}

.macbook-pro-14-1 .text {
  color: var(--ocean-green);
  font-family: var(--font-family-inter);
  font-size: var(--font-size-l);
  font-weight: 700;
  letter-spacing: 0;
  line-height: normal;
  min-height: 35px;
  text-align: center;
  width: 34px;
}

.macbook-pro-14-1 .button_-container-1 {
  align-items: flex-start;
  display: flex;
  flex-direction: column;
  gap: 38px;
  margin-bottom: 76.0px;
  margin-left: 33px;
  min-height: 318px;
  width: 52px;
}

.macbook-pro-14-1 .x2 {
  left: 0;
  letter-spacing: 0;
  line-height: normal;
  position: absolute;
  text-align: center;
  top: 9px;
  width: 51px;
}

.macbook-pro-14-1 .overlap-group7 {
  align-items: flex-end;
  background-color: var(--gallery);
  border-radius: 102px 0px 0px 102px;
  display: flex;
  height: 900px;
  justify-content: flex-end;
  margin-left: 113px;
  min-width: 1171px;
  padding: 58px 59px;
}

.macbook-pro-14-1 .your-text {
  color: #33363fb8;
  font-family: var(--font-family-inter);
  font-size: var(--font-size-m);
  font-weight: 700;
  letter-spacing: 0;
  line-height: normal;
  min-height: 781px;
  width: 1026px;
}

.macbook-pro-14-1 .button-3 {
  background-color: var(--tuna);
  border-radius: 18px;
  display: flex;
  height: 51px;
  min-width: 51px;
  
}

.macbook-pro-14-1 .x15-4 {
  left: 0;
  letter-spacing: 0;
  line-height: normal;
  position: absolute;
  text-align: center;
  top: 9px;
  width: 51px;
}

:root { 
  --black: #000000;
  --gallery: #eeeded;
  --ocean-green: #34b175;
  --tuna: #33363f;
  --white: #ffffff;
 
  --font-size-l: 30px;
  --font-size-m: 24px;
  --font-size-s: 22px;
  --font-size-xl: 48px;
 
  --font-family-inter: "Inter", Helvetica;
  --font-family-italiana: "Italiana", Helvetica;
}
.inter-bold-ocean-green-24px {
  color: var(--ocean-green);
  font-family: var(--font-family-inter);
  font-size: var(--font-size-m);
  font-style: normal;
  font-weight: 700;
}

.inter-bold-ocean-green-22px {
  color: var(--ocean-green);
  font-family: var(--font-family-inter);
  font-size: var(--font-size-s);
  font-style: normal;
  font-weight: 700;
}

.inter-bold-white-24px {
  color: var(--white);
  font-family: var(--font-family-inter);
  font-size: var(--font-size-m);
  font-style: normal;
  font-weight: 700;
}


</style>

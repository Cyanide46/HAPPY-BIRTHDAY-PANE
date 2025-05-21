
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Birthday Surprise</title>
  <style>
    body {
      font-family: 'Comic Sans MS', cursive, sans-serif;
      background: linear-gradient(to bottom right, #ffe8ec, #e0f7fa);
      display: flex;
      flex-direction: column;
      align-items: center;
      padding: 20px;
    }
    h1 {
      font-size: 2em;
      margin-bottom: 20px;
      animation: bounce 1s infinite alternate;
    }
    @keyframes bounce {
      0% { transform: translateY(0); }
      100% { transform: translateY(-10px); }
    }
    .card, .form {
      background: white;
      border-radius: 15px;
      padding: 20px;
      box-shadow: 0 4px 12px rgba(0,0,0,0.2);
      max-width: 400px;
      width: 100%;
      margin-top: 20px;
    }
    input, select, textarea, button {
      display: block;
      width: 100%;
      margin: 10px 0;
      padding: 10px;
      border-radius: 8px;
      border: 1px solid #ccc;
      font-size: 1em;
    }
    img {
      max-width: 100%;
      border-radius: 10px;
      margin: 10px 0;
    }
    .hidden {
      display: none;
    }
    .graphics {
      font-size: 2rem;
      text-align: center;
      margin-bottom: 10px;
    }
  </style>
</head>
<body>
  <h1 id="title">Create a Surprise Birthday Card</h1>

  <div class="form" id="form">
    <input type="text" id="name" placeholder="Recipient's Name" />
    <input type="number" id="age" placeholder="Recipient's Age" />
    <textarea id="message" placeholder="Birthday message"></textarea>
    <input type="file" id="photo" accept="image/*"/>
    <select id="design">
      <option value="classic">Classic</option>
      <option value="fun">Fun</option>
      <option value="modern">Modern</option>
    </select>
    <button onclick="saveData()">Generate Link for Celebrant</button>
  </div>

  <div class="card hidden" id="questionCard">
    <p><strong>Answer this to unlock your birthday surprise:</strong></p>
    <p id="questionText"></p>
    <input type="text" id="answer" placeholder="Your answer..." />
    <button onclick="checkAnswer()">Submit</button>
  </div>

  <div class="card hidden" id="ecard">
    <div class="graphics" id="graphics">🎉🎂🎈</div>
    <h2 id="ecardTitle"></h2>
    <img id="photoPreview" src="" class="hidden" />
    <p id="ecardMessage"></p>
  </div>

  <script>
    const questions = [
      "What’s the opposite of sadness on your birthday?",
      "What comes with cake, balloons, and laughter?",
      "What’s your favorite birthday treat?"
    ];
    const answers = ["fun", "party", "cake"];
    let selectedQuestion = "";

    function saveData() {
      const name = document.getElementById('name').value;
      const age = document.getElementById('age').value;
      const message = document.getElementById('message').value;
      const design = document.getElementById('design').value;
      const photoInput = document.getElementById('photo');
      if (!name || !age) {
        alert("Please enter name and age.");
        return;
      }

      localStorage.setItem("bday_name", name);
      localStorage.setItem("bday_age", age);
      localStorage.setItem("bday_msg", message);
      localStorage.setItem("bday_design", design);

      if (photoInput.files.length > 0) {
        const reader = new FileReader();
        reader.onload = function(e) {
          localStorage.setItem("bday_photo", e.target.result);
          lockAndReload();
        };
        reader.readAsDataURL(photoInput.files[0]);
      } else {
        lockAndReload();
      }
    }

    function lockAndReload() {
      localStorage.setItem("bday_locked", "yes");
      alert("Your birthday surprise is ready! Send this file to the celebrant.");
      location.reload();
    }

    function startCelebrantFlow() {
      selectedQuestion = questions[Math.floor(Math.random() * questions.length)];
      document.getElementById("questionText").innerText = selectedQuestion;
      document.getElementById("questionCard").classList.remove("hidden");
    }

    function checkAnswer() {
      const userAnswer = document.getElementById('answer').value.toLowerCase();
      if (answers.some(ans => userAnswer.includes(ans))) {
        showCard();
      } else {
        alert("Oops! Try again with a birthday-themed answer!");
      }
    }

    function showCard() {
      document.getElementById('questionCard').classList.add('hidden');
      const name = localStorage.getItem("bday_name");
      const age = localStorage.getItem("bday_age");
      const msg = localStorage.getItem("bday_msg") || "Have a great day!";
      const design = localStorage.getItem("bday_design");
      const photo = localStorage.getItem("bday_photo");

      const graphics = {
        classic: "🎂✨👑🍭",
        fun: "🎉🍬🎈🎊",
        modern: "🎁🌟🎇💎"
      };

      document.getElementById("ecardTitle").innerText = `Happy Birthday, ${name}!`;
      document.getElementById("ecardMessage").innerText = `${msg} You are now ${age} years old!`;
      document.getElementById("graphics").innerText = graphics[design];

      if (photo) {
        const img = document.getElementById('photoPreview');
        img.src = photo;
        img.classList.remove('hidden');
      }

      document.getElementById("ecard").classList.remove("hidden");
    }

    // On load check if it's locked
    window.onload = () => {
      if (localStorage.getItem("bday_locked") === "yes") {
        document.getElementById("form").classList.add("hidden");
        document.getElementById("title").innerText = "You Have a Surprise!";
        startCelebrantFlow();
      }
    };
  </script>
</body>
</html>

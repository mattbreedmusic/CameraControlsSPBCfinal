# CameraControlsSPBCfinal
<!DOCTYPE html>
<html>

<head>
  <!-- <meta name="viewport" content="width=device-width, initial-scale=1" /> -->
  <title>Custom Companion Stream Deck</title>
  <meta charset="utf-8"/>
  <style>
    @media (orientation: portrait) {
      .portrait-warning {
        position: fixed;
        box-sizing: border-box;
        padding: 40px;
        width: 100vw;
        height: 100vh;
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
        top: 0;
        left: 0;
        font-size: 80px;
        z-index: 1;
        background: rgba(0, 0, 0, 0.1);
        backdrop-filter: blur(2px) grayscale(0.9);
      }
      .portrait-warning button {
        font-size: 50px;
      }
    }
     @media (orientation: landscape) {
      .portrait-warning {
        display: none;
      }
     }
    * {
      -webkit-user-select: none; /* Safari */
      -ms-user-select: none;     /* IE10+ */
      user-select: none;         /* Standard */
    }
    tr > :last-child {
      position: sticky;
      right: 0;
    }
    body {
      background: #6384b5;
      color: white;
      font-family: montserrat, classic;
      margin: 0;
      padding: 0;
      overflow-x: hidden;
      min-width: max(100vw, 900px);
      overflow: auto;
    }

    h1 {
      text-align: center;
      font-size: 30px;
      margin: 10px 0;
      font-weight: bold;
    }

    table {
      border-collapse: collapse;
      margin: auto;
      height: calc(100vh - 80px);
      width: 100%;
      min-height: calc(100vh - 80px);
      table-layout: fixed;
    }

    th,
    td {
      border: 1px solid #6385b5;
      text-align: center;
      vertical-align: middle;
      padding: 1px;
    }

    th {
      background: #364b5c;
      font-size: 20px;
      padding: 5px;
    }

    .row-label {
      background: #364b5c;
      font-size: 20px;
      width: 100px;
    }
  

    /* Burgundy half-width buttons */
    .button {
      width: 48%;
      height: 96%;
      font-size: 16px;
      border: 3px solid #800020;
      outline: none;
      color: white;
      cursor: pointer;
      background: #800020;
      border-radius: 12px;
      margin: 1%;
      transition: background 0.2s, box-shadow 0.2s, border 0.2s;
    }

    /* Full-width burgundy (Slide) */
    .button-full {
      width: 100%;
      height: 100%;
      font-size: 20px;
      outline: none;
      color: white;
      cursor: pointer;
      background: #800020;
      border: 3px solid #800020;
      border-radius: 10px;
      transition: background 0.2s, box-shadow 0.2s, border 0.2s;
    }

    /* Selected (dark center, thin light border) */
    .button.selected,
    .button-full.selected {
      background: #4d0014;
      box-shadow: inset 0 0 50px #800020;
    }

    .button-stack {
      display: flex;
      justify-content: space-between;
      flex-wrap: wrap;
      height: 100%;
      width: 100%;
    }

    /* Edit / utility buttons (rounded) */
    .edit-button {
      width: 100%;
      height: 100%;
      font-size: 20px;
      border: none;
      outline: none;
      color: black;
      cursor: pointer;
      background: #fea02f;
      font-weight: bold;
      margin: 2px 0;
      border-radius: 10px;
    }

    .goLive-button {
      background: #5cb85c;
    }

    /* Fade action button (distinct green) */
    .fade-button {
      background: #3cb371;
      color: white;
      border-radius: 10px;
      font-weight: bold;
    }

    .live-button {
      background: #5cb85c;
      color: rgb(0, 0, 0);
      border-radius: 10px;
      font-weight: bold;
    }

    .live-button:hover {
      background: #5cb85c;
    }

    .fade-button:hover {
      background: #2e8b57;
    }

    /* Committed (faded) button turns green */
    .faded {
      background: #3cb371 !important;
      color: white !important;
      border: 2px solid #2e8b57 !important;
      box-shadow: none !important;
    }

    .dark-faded {
      background: #4d0014 !important;
      border: 3px solid #800020 !important;
      color: white !important;
      box-shadow: inset 0 0 50px #800020 !important;
    }

    /* Faint veil for whole column (works for th and td) */
    .col-faded {
      background-color: rgba(60, 179, 113, 0.5);
      cursor: not-allowed; /* Makes the cursor show that the button is disabled */
    }
    .col-faded button:not(.faded) {
      filter: grayscale(1) blur(1px); /* Apply some visual effect to the disabled buttons */
      pointer-events: none; /* makes the buttons ignore clicks */
    }

    /* Edit side panel */
    #editPanel {
      position: fixed;
      top: 0;
      right: 0;
      width: 500px;
      height: 100%;
      background: rgba(32, 32, 32, 0.7);
      backdrop-filter: blur(2px);
      color: white;
      transform: translateX(100%);
      transition: transform 0.4s ease;
      padding: 30px;
      overflow-y: auto;
      z-index: 1000;
      display: none;
    }

    #editPanel.open {
      display: block;
      transform: translateX(0);
    }

    #editPanel h2 {
      margin-top: 0;
    }

    .edit-grid {
      display: grid;
      grid-template-columns: repeat(4, 1fr);
      grid-template-rows: repeat(3, 100px);
      gap: 12px;
      margin-top: 20px;
    }

    .edit-grid button {
      background: #555;
      border: none;
      color: white;
      font-size: 30px;
      border-radius: 8px;
      cursor: pointer;
    }

    .close-btn {
      margin-top: 20px;
      padding: 12px;
      width: 100%;
      font-size: 16px;
      background: #f44336;
      color: white;
      border: none;
      border-radius: 6px;
      cursor: pointer;
    }

    /* Go Live modal */
    #goLiveModal {
      display: none;
      position: fixed;
      top: 0;
      left: 0;
      width: 100vw;
      height: 100vh;
      background: rgba(0, 0, 0, 0.7);
      z-index: 2000;
      justify-content: center;
      align-items: center;
    }

    #goLiveModalContent {
      background: #222;
      padding: 30px;
      border-radius: 10px;
      text-align: center;
      min-width: 300px;
    }

    #goLiveModalContent button {
      margin: 10px;
      padding: 10px 20px;
      font-size: 16px;
      border-radius: 6px;
      border: none;
      cursor: pointer;
    }

    #goLiveModalContent .yes-btn {
      background: #5cb85c;
      color: white;
    }

    #goLiveModalContent .no-btn {
      background: #f44336;
      color: white;
    }

  </style>
</head>
<body>
  <h1>Sunday Morning Cameras</h1>
  <div class="portrait-warning">
    <div>This application works best in landscape. Please Rotate</div>
    <button onclick="document.querySelector('.portrait-warning').remove()">Dismiss</button>
  </div>

  <table>
    <thead>
      <tr>
        <th></th>
        <th>PTZ1</th>
        <th>PTZ2</th>
        <th>PTZ3</th>
        <th></th>
      </tr>
    </thead>
    <tbody id="buttonGrid"></tbody>
  </table>

  <div id="editPanel">
    <h2 id="editTitle">Edit Mode</h2>
    <div class="edit-grid" id="editGrid"></div>
    <button class="close-btn" onclick="closeEditPanel()">Close</button>
  </div>

  <div id="goLiveModal">
    <div id="goLiveModalContent">
      <h2>Are you sure?</h2>
      <button id="goLiveYes" class="yes-btn" onclick="confirmGoLive()">Yes</button>
      <button class="no-btn" onclick="closeGoLiveModal()">No</button>
    </div>
  </div>

  <script>
    let isLive = false; // Track livestream state
    const columnLabels = ["PTZ1", "PTZ2", "PTZ3"];
    const rowLabels = ["Lectern", "WL", "CO-WL", "Drums & Bass", "Wide", "Other"];
    const placeholderNames = [
      ["Lectern", "Lectern Close"],
      ["WL", "WL Close"],
      ["Co-WL", "Co-WL Close"],
      ["Drummer", "Drummer Close"],
      ["Wide", "Wide 2"],
      ["Other", "Other 2"]
    ];


    const grid = document.getElementById("buttonGrid");

    let toggledButton = null;
    let lastFadedColumn = null;
    let selectedButton = null;
    let previousButton = null;

    /** 🔑 Map each button ID to its function */
    const buttonClickMap = {
      "btn-1-1-L": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/10/0/1/press", {
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "btn-1-1-R": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/10/1/1/press", {
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "btn-2-1-L": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/10/0/2/press", {
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "btn-2-1-R": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/10/1/2/press", {
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "btn-3-1-L": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/10/2/1/press", {
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "btn-3-1-R": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/10/3/1/press", { //needed in companion//
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "btn-4-1-L": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/10/4/1/press", {
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "btn-4-1-R": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/10/4/2/press", {
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "btn-5-1-L": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/10/1/3/press", {
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "btn-5-1-R": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/10/0/3/press", {
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "btn-6-1-L": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/10/1/4/press", {
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "btn-6-1-R": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/10/0/4/press", {
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "btn-1-2-L": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/11/1/2/press", {
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "btn-1-2-R": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/11/2/2/press", {
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "btn-2-2-L": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/11/0/2/press", {
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "btn-2-2-R": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/11/1/4/press", {
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "btn-3-2-L": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/11/0/4/press", {
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "btn-3-2-R": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/11/0/5/press", {
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "btn-4-2-L": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/11/0/3/press", {
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "btn-4-2-R": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/11/1/3/press", {
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "btn-5-2-L": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/11/0/1/press", {
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "btn-5-2-R": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/11/3/1/press", {
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "btn-6-2-L": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/11/1/1/press", {
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "btn-6-2-R": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/11/1/5/press", {
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "btn-1-3-L": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/12/0/4/press", {
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "btn-1-3-R": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/12/0/1/press", {
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "btn-2-3-L": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/12/0/3/press", {
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "btn-2-3-R": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/12/0/6/press", {
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "btn-3-3-L": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/12/1/3/press", {
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "btn-3-3-R": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/12/1/5/press", {
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "btn-4-3-L": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/12/0/2/press", {
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "btn-4-3-R": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/12/3/2/press", {
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "btn-5-3-L": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/12/2/2/press", {
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "btn-5-3-R": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/12/2/1/press", {
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "btn-6-3-L": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/12/1/4/press", {
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "btn-6-3-R": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/12/1/2/press", {
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },

      "btn-slide": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/10/2/3/press", {
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "btn-fade": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/10/2/4/press", {
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      
     "edit-PTZ1-12": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/9/3/4/press", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
       "edit-PTZ2-12": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/13/3/4/press", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
       "edit-PTZ3-12": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/14/3/4/press", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },

      "goLiveYes": function triggerCompanion() {
        console.log("in the buttonClickMap")
        fetch("http://192.168.1.16:8000/api/location/2/1/4/press", { //not working yet//
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
        if (isLive) {
          console.log("Alive!")
        } else {
          console.log("Dead :(")
        }
      },

      // Add more like this:
      // "btn-1-1-R": () => console.log("Row 1 Col 1 Right clicked"),
      // "btn-2-2-L": () => console.log("Row 2 Col 2 Left clicked"),
    };

    const buttonMousedownMap = {
      "edit-PTZ1-1": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/9/0/1/down", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
        "edit-PTZ1-2": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/9/0/2/down", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
        "edit-PTZ1-3": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/9/0/3/down", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
        "edit-PTZ1-4": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/9/0/4/down", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
        "edit-PTZ1-5": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/9/1/1/down", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
        "edit-PTZ1-7": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/9/1/3/down", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
        "edit-PTZ1-8": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/9/1/4/down", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
  "edit-PTZ1-9": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/9/2/1/down", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
        "edit-PTZ1-10": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/9/2/2/down", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
  "edit-PTZ1-11": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/9/2/3/down", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
        "edit-PTZ2-1": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/13/0/1/down", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "edit-PTZ2-2": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/13/0/2/down", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "edit-PTZ2-3": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/13/0/3/down", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "edit-PTZ2-4": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/13/0/4/down", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "edit-PTZ2-5": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/13/1/1/down", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "edit-PTZ2-7": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/13/1/3/down", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
       "edit-PTZ2-8": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/13/1/4/down", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
       "edit-PTZ2-9": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/13/2/1/down", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
       "edit-PTZ2-10": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/13/2/2/down", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
       "edit-PTZ2-11": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/13/2/3/down", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
       "edit-PTZ3-1": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/14/0/1/down", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "edit-PTZ3-2": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/14/0/2/down", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "edit-PTZ3-3": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/14/0/3/down", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "edit-PTZ3-4": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/14/0/4/down", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "edit-PTZ3-5": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/14/1/1/down", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "edit-PTZ3-7": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/14/1/3/down", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
       "edit-PTZ3-8": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/14/1/4/down", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
       "edit-PTZ3-9": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/14/2/1/down", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
       "edit-PTZ3-10": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/14/2/2/down", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
       "edit-PTZ3-11": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/14/2/3/down", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
    }
    const buttonTouchstartMap = {
      "edit-PTZ1-1": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/9/0/1/down", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
        "edit-PTZ1-2": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/9/0/2/down", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
        "edit-PTZ1-3": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/9/0/3/down", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
        "edit-PTZ1-4": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/9/0/4/down", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
        "edit-PTZ1-5": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/9/1/1/down", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
        "edit-PTZ1-7": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/9/1/3/down", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
        "edit-PTZ1-8": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/9/1/4/down", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
  "edit-PTZ1-9": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/9/2/1/down", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
        "edit-PTZ1-10": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/9/2/2/down", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
  "edit-PTZ1-11": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/9/2/3/down", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
        "edit-PTZ2-1": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/13/0/1/down", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "edit-PTZ2-2": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/13/0/2/down", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "edit-PTZ2-3": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/13/0/3/down", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "edit-PTZ2-4": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/13/0/4/down", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "edit-PTZ2-5": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/13/1/1/down", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "edit-PTZ2-7": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/13/1/3/down", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
       "edit-PTZ2-8": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/13/1/4/down", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
       "edit-PTZ2-9": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/13/2/1/down", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
       "edit-PTZ2-10": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/13/2/2/down", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
       "edit-PTZ2-11": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/13/2/3/down", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
       "edit-PTZ3-1": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/14/0/1/down", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "edit-PTZ3-2": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/14/0/2/down", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "edit-PTZ3-3": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/14/0/3/down", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "edit-PTZ3-4": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/14/0/4/down", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "edit-PTZ3-5": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/14/1/1/down", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "edit-PTZ3-7": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/14/1/3/down", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
       "edit-PTZ3-8": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/14/1/4/down", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
       "edit-PTZ3-9": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/14/2/1/down", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
       "edit-PTZ3-10": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/14/2/2/down", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
       "edit-PTZ3-11": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/14/2/3/down", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
    }

    const buttonMouseupMap = {
      "edit-PTZ1-1": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/9/0/1/up", {
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
        "edit-PTZ1-2": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/9/0/2/up", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
        "edit-PTZ1-3": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/9/0/3/up", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
        "edit-PTZ1-4": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/9/0/4/up", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
        "edit-PTZ1-5": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/9/1/1/up", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
        "edit-PTZ1-7": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/9/1/3/up", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
        "edit-PTZ1-8": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/9/1/4/up", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
  "edit-PTZ1-9": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/9/2/1/up", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
        "edit-PTZ1-10": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/9/2/2/up", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
  "edit-PTZ1-11": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/9/2/3/up", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
        "edit-PTZ2-1": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/13/0/1/up", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "edit-PTZ2-2": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/13/0/2/up", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "edit-PTZ2-3": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/13/0/3/up", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "edit-PTZ2-4": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/13/0/4/up", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "edit-PTZ2-5": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/13/1/1/up", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "edit-PTZ2-7": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/13/1/3/up", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
       "edit-PTZ2-8": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/13/1/4/up", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
       "edit-PTZ2-9": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/13/2/1/up", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
       "edit-PTZ2-10": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/13/2/2/up", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
       "edit-PTZ2-11": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/13/2/3/up", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
       "edit-PTZ3-1": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/14/0/1/up", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "edit-PTZ3-2": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/14/0/2/up", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "edit-PTZ3-3": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/14/0/3/up", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "edit-PTZ3-4": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/14/0/4/up", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "edit-PTZ3-5": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/14/1/1/up", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "edit-PTZ3-7": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/14/1/3/up", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
       "edit-PTZ3-8": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/14/1/4/up", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
       "edit-PTZ3-9": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/14/2/1/up", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
       "edit-PTZ3-10": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/14/2/2/up", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
       "edit-PTZ3-11": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/14/2/3/up", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
    }
    const buttonTouchendMap = {
      "edit-PTZ1-1": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/9/0/1/up", {
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
        "edit-PTZ1-2": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/9/0/2/up", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
        "edit-PTZ1-3": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/9/0/3/up", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
        "edit-PTZ1-4": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/9/0/4/up", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
        "edit-PTZ1-5": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/9/1/1/up", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
        "edit-PTZ1-7": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/9/1/3/up", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
        "edit-PTZ1-8": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/9/1/4/up", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
  "edit-PTZ1-9": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/9/2/1/up", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
        "edit-PTZ1-10": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/9/2/2/up", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
  "edit-PTZ1-11": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/9/2/3/up", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
        "edit-PTZ2-1": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/13/0/1/up", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "edit-PTZ2-2": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/13/0/2/up", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "edit-PTZ2-3": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/13/0/3/up", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "edit-PTZ2-4": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/13/0/4/up", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "edit-PTZ2-5": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/13/1/1/up", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "edit-PTZ2-7": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/13/1/3/up", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
       "edit-PTZ2-8": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/13/1/4/up", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
       "edit-PTZ2-9": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/13/2/1/up", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
       "edit-PTZ2-10": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/13/2/2/up", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
       "edit-PTZ2-11": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/13/2/3/up", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
       "edit-PTZ3-1": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/14/0/1/up", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "edit-PTZ3-2": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/14/0/2/up", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "edit-PTZ3-3": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/14/0/3/up", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "edit-PTZ3-4": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/14/0/4/up", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "edit-PTZ3-5": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/14/1/1/up", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
      "edit-PTZ3-7": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/14/1/3/up", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
       "edit-PTZ3-8": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/14/1/4/up", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
       "edit-PTZ3-9": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/14/2/1/up", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
       "edit-PTZ3-10": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/14/2/2/up", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
       "edit-PTZ3-11": function triggerCompanion() {
        fetch("http://192.168.1.16:8000/api/location/14/2/3/up", { 
          method: "POST",
          mode: "no-cors"
        }).catch(err => console.error(err));
      },
    }

    const buttonNameMap = {
      "btn-1-1-L": "Lectern",
      "btn-1-1-R": "Lectern Close",
      "btn-1-2-L": "Lectern",
      "btn-1-2-R": "Lectern Close",
      "btn-1-3-L": "Lectern",
      "btn-1-3-R": "Lectern Close",
      "btn-2-1-L": "Worship Leader",
      "btn-2-1-R": "WL Close",
      "btn-2-2-L": "Worship Leader",
      "btn-2-2-R": "WL Close",
      "btn-2-3-L": "Worship Leader",
      "btn-2-3-R": "WL Close",
      "btn-3-1-L": "Co-WL",
      "btn-3-1-R": "Co-WL Close",
      "btn-3-2-L": "Co-WL",
      "btn-3-2-R": "Co-WL Close",
      "btn-3-3-L": "Co-WL",
      "btn-3-3-R": "Co-WL Close",
      "btn-4-1-L": "Drummer",
      "btn-4-1-R": "Bass Close",
      "btn-4-2-L": "Drummer",
      "btn-4-2-R": "Drummer Close",
      "btn-4-3-L": "Bass and WL",
      "btn-4-3-R": "Bass Close",
      "btn-5-1-L": "Wide Stage",
      "btn-5-1-R": "Wide Stage Right",
      "btn-5-2-L": "Wide 1",
      "btn-5-2-R": "Wide Stage Left",
      "btn-5-3-L": "Wide 1",
      "btn-5-3-R": "Communion Above",
      "btn-6-1-L": "Communion",
      "btn-6-1-R": "Communion Wide",
      "btn-6-2-L": "Baptistry Front",
      "btn-6-2-R": "Kids Corner",
      "btn-6-3-L": "Fire escape",
      "btn-6-3-R": "Baptistry Above"
    }

    const missingNames = {}
    /** Build table */
    rowLabels.forEach((rowLabel, row) => {
      const tr = document.createElement("tr");

      const th = document.createElement("th");
      th.className = "row-label";
      th.innerText = rowLabel;
      tr.appendChild(th);

      columnLabels.forEach((_, col) => {
        const td = document.createElement("td");
        const stack = document.createElement("div");
        stack.className = "button-stack";

        ["L", "R"].forEach((side, idx) => {
          const btn = document.createElement("button");
          btn.className = "button";
          btn.id = `btn-${row + 1}-${col + 1}-${side}`;
          // btn.innerText = placeholderNames[row][idx] || "—";
          btn.innerText = buttonNameMap[btn.id] || `MISSING ${btn.id}`
          if (!buttonNameMap[btn.id]) {
            missingNames[btn.id] = "ADD NAME HERE"
          }


          // Attach per-button function if it exists, otherwise default
          btn.addEventListener("click", (e) => {
            e.stopPropagation();
            console.log("Line 586")
            if (buttonClickMap[btn.id]) {
              buttonClickMap[btn.id](e); // run custom function
            }
            handleSelection(btn); // still manage selection state
          });

          stack.appendChild(btn);
        });

        td.appendChild(stack);
        tr.appendChild(td);
      });


      // === Right-side utility buttons ===
      const rightTd = document.createElement("td");
      if (row < 3) {
        const editBtn = document.createElement("button");
        editBtn.className = "edit-button";
        editBtn.innerText = columnLabels[row] + " Edit";
        editBtn.onclick = () => openEditPanel(columnLabels[row]);
        rightTd.appendChild(editBtn);
      } else if (row === 3) {
        const goLiveBtn = document.createElement("button");
        goLiveBtn.className = "edit-button goLive-button";
        goLiveBtn.innerText = "DSK TOGGLE";
        goLiveBtn.onclick = openGoLiveModal;
        rightTd.appendChild(goLiveBtn);
      } else if (row === 4) {
        const slideBtn = document.createElement("button");
        slideBtn.className = "button-full";
        slideBtn.id = "btn-slide";   // 🔑 unique ID
        slideBtn.innerText = "Slide";
        slideBtn.onclick = (e) => {
          if (buttonClickMap["btn-slide"]) buttonClickMap["btn-slide"](e);
          console.log("aefof")
          handleSlide();
        };
        rightTd.appendChild(slideBtn);
        window.slideBtn = slideBtn;
      } else if (row === 5) {
        const fadeBtn = document.createElement("button");
        fadeBtn.className = "edit-button fade-button";
        fadeBtn.innerText = "Fade";
        fadeBtn.id = "btn-fade";   // 🔑 unique ID
        fadeBtn.onclick = (e) => {
          if (buttonClickMap["btn-fade"]) buttonClickMap["btn-fade"](e);
          applyFade(); // keep your existing fade logic
        };
        rightTd.appendChild(fadeBtn);
      }

      tr.appendChild(rightTd);
      grid.appendChild(tr);
    });

    console.log(JSON.stringify(missingNames, null, 2))

    /** Selection handling */
    function handleSelection(target) {
      document.querySelectorAll(".button.selected, .button-full.selected, .dark-faded")
        .forEach(el => el.classList.remove("selected", "dark-faded"));
      target.classList.add("selected");
      selectedButton = target;
    }

    // === existing functions (editPanel, goLiveModal, applyFade, handleSlide, etc.) stay the same ===

    // Edit panel
    const editPanel = document.getElementById("editPanel");
    const editTitle = document.getElementById("editTitle");
    const editGrid = document.getElementById("editGrid");
    function openEditPanel(ptzName) {
      console.log("Running openEditPanel")
      editTitle.innerText = ptzName + " - Edit Mode";
      editGrid.innerHTML = "";

      const buttonLayout = [
        { type: "arrow", dir: "nw" },
        { type: "arrow", dir: "n" },
        { type: "arrow", dir: "ne" },
        { type: "text", label: "Zoom In" },
        { type: "arrow", dir: "w" },
        { type: "empty" },
        { type: "arrow", dir: "e" },
        { type: "text", label: "Zoom Out" },
        { type: "arrow", dir: "sw" },
        { type: "arrow", dir: "s" },
        { type: "arrow", dir: "se" },
        { type: "text", label: "Focus" }
      ];
      ["a", "b", "c", "d"].forEach(function (letter, index) {
        console.log("In the forEach loop", { letter, index })
      })
      buttonLayout.forEach((cfg, index) => {
        const b = document.createElement("button");

        // Assign ID like: edit-PTZ1-1, edit-PTZ1-2, etc.
        b.id = `edit-${ptzName}-${index + 1}`;

        if (cfg.type === "arrow") {
          // Use Unicode arrows for simplicity
          const arrows = {
            n: "↑",
            ne: "↗",
            e: "→",
            se: "↘",
            s: "↓",
            sw: "↙",
            w: "←",
            nw: "↖"
          };
          b.innerText = arrows[cfg.dir];
        } else if (cfg.type === "text") {
          b.innerText = cfg.label;
        } else if (cfg.type === "empty") {
          b.style.visibility = "hidden"; // placeholder
        }
        b.onclick = (e) => {
          const fn = buttonClickMap[b.id];
          if (fn) fn(e);
        };
        b.addEventListener("mousedown", (e) => {
          const fn = buttonMousedownMap[b.id];
          if (fn) fn(e);
        })
        b.addEventListener("mouseup", (e) => {
          const fn = buttonMouseupMap[b.id];
          if (fn) fn(e);
        })
            b.addEventListener("touchstart", (e) => {
          const fn = buttonTouchstartMap[b.id];
          if (fn) fn(e);
        })
        b.addEventListener("touchend", (e) => {
          const fn = buttonTouchendMap[b.id];
          if (fn) fn(e);
        })
        editGrid.appendChild(b);
      });
      

      editPanel.style.display = "block";
      setTimeout(() => editPanel.classList.add("open"), 10);
    }

    function closeEditPanel() {
      editPanel.classList.remove("open");
      setTimeout(() => { editPanel.style.display = "none"; }, 400);
    }

    // Go live modal
    function openGoLiveModal() { document.getElementById("goLiveModal").style.display = "flex"; }
    function closeGoLiveModal() { document.getElementById("goLiveModal").style.display = "none"; }
    function confirmGoLive() { alert("Yes clicked!"); }
    goLiveYes.addEventListener("click", (e) => {
      if (buttonClickMap["goLiveYes"]) buttonClickMap["goLiveYes"](e);
      closeGoLiveModal();
    })



    // Selection
    grid.addEventListener("click", (e) => {
      const target = e.target;
      if (target.classList.contains("button") || target.classList.contains("button-full")) {
        document.querySelectorAll(".button.selected, .button-full.selected")
          .forEach(el => el.classList.remove("selected"));
        target.classList.add("selected");
        selectedButton = target;
      }
    });

    function confirmGoLive() {
      console.log("in confirm go live")
      if (!isLive) {
        const content = document.getElementById("goLiveModalContent");
        content.querySelector("h2").innerText = "Are you sure?";
        // Starting livestream
        isLive = true;
        const goLiveBtn = document.querySelector(".goLive-button");
        goLiveBtn.classList.remove("goLive-button");
        goLiveBtn.classList.add("live-button");
        goLiveBtn.innerText = "DSK TOGGLE";
        goLiveBtn.onclick = confirmStopLive; // change action to stop
        closeGoLiveModal();
      }
    }

    // When clicking red button (stop request)
    function confirmStopLive() {
      const modal = document.getElementById("goLiveModal");
      const content = document.getElementById("goLiveModalContent");
      modal.style.display = "flex";

      content.querySelector("h2").innerText = "Are you sure?";

      const yesBtn = content.querySelector(".yes-btn");
      const noBtn = content.querySelector(".no-btn");

      // Reset old listeners
      yesBtn.onclick = () => {
        isLive = false;
        const goLiveBtn = document.querySelector(".live-button");
        goLiveBtn.classList.remove("live-button");
        goLiveBtn.classList.add("goLive-button");
        goLiveBtn.innerText = "DSK TOGGLE";
        goLiveBtn.onclick = openGoLiveModal; // reset action
        modal.style.display = "none";
      };

      noBtn.onclick = () => { modal.style.display = "none"; };
    }


    function clearColumnVeil() {
      document.querySelectorAll(".col-faded").forEach(cell =>
        cell.classList.remove("col-faded")
      );
    }

    function veilColumnByIndex(colIndex) {
      document.querySelectorAll(`thead th:nth-child(${colIndex + 1}), tbody td:nth-child(${colIndex + 1})`)
        .forEach(cell => cell.classList.add("col-faded"));
    }

    // Fade button logic

    function clearAllDark() {
      document.querySelectorAll('.dark-faded').forEach(b => b.classList.remove('dark-faded'));
      // previousButton = null;
    }

    function toggleButton(btn) {
      
      // Reset currently selected button
      if (selectedButton && selectedButton !== btn) {
        selectedButton.classList.remove("selected");
      }

      // Clear any previously faded button if we're selecting a new one
      if (toggledButton && toggledButton !== btn) {
        
        toggledButton.classList.remove("faded");
        toggledButton = null;
      }

      // Toggle the clicked button
      if (btn.classList.contains("selected")) {
        btn.classList.remove("selected");
        selectedButton = null;
      } else {
        btn.classList.add("selected");
        selectedButton = btn;
      }
    }

    function applyFade() {
      // If nothing selected, treat as "toggle between the two"
      if (!selectedButton) {
        document.querySelectorAll(".faded").forEach(b => b.classList.remove("faded"));
        if (toggledButton && previousButton) {
          // swap roles: previous -> green, toggled -> dark
          toggledButton.classList.remove('faded');
          toggledButton.classList.add('dark-faded');

          previousButton.classList.remove('dark-faded');
          previousButton.classList.add('faded');

          const tmp = toggledButton;
          toggledButton = previousButton;
          previousButton = tmp;

          updateVeilForButton(toggledButton);
        }
        return;
      }

      // If selected is the currently green one and there's a previous, swap
      if (selectedButton === toggledButton && previousButton) {
        
        document.querySelectorAll(".faded").forEach(b => b.classList.remove("faded"));
        toggledButton.classList.remove('faded');
        toggledButton.classList.add('dark-faded');

        previousButton.classList.remove('dark-faded');
        previousButton.classList.add('faded');

        const tmp = toggledButton;
        toggledButton = previousButton;
        toggledButton = selectedButton;
        previousButton = tmp;

        updateVeilForButton(toggledButton);
        selectedButton = null;
        return;
      }

      // If selected is the previous (dark) button — swap to make it green
      if (selectedButton === previousButton) {
        
        document.querySelectorAll(".faded").forEach(b => b.classList.remove("faded"));
        previousButton.classList.remove('dark-faded');
        previousButton.classList.add('faded');

        if (toggledButton) {
          toggledButton.classList.remove('faded');
          toggledButton.classList.add('dark-faded');
        }

        const tmp = toggledButton;
        toggledButton = previousButton;
        previousButton = tmp;

        updateVeilForButton(toggledButton);
        selectedButton = null;
        return;
      }

      // Selecting a new (third) button:
      // demote current green -> dark, move it into previousButton
      if (toggledButton && toggledButton !== selectedButton) {
        
        document.querySelectorAll(".faded").forEach(b => b.classList.remove("faded"));
        document.querySelectorAll(".dark-faded").forEach(b => b.classList.remove("dark-faded"));
        toggledButton.classList.remove('faded');
        toggledButton.classList.add('dark-faded');
        previousButton = toggledButton;
      }

      // Make selected green
      selectedButton.classList.remove('selected', 'dark-faded');
      selectedButton.classList.add('faded');
      toggledButton = selectedButton;

      updateVeilForButton(toggledButton);

      // clear selection so subsequent Fade can toggle without re-click
      selectedButton = null;
      if (!selectedButton) return;

      // Clear any previously darkened button (only 1 allowed)
      
      document.querySelectorAll(".faded").forEach(b => b.classList.remove("faded"));

      // Commit new button
      selectedButton.classList.remove("selected");
      selectedButton.classList.add("faded");
      toggledButton = selectedButton;

      // Remove old veil before applying new one
      clearColumnVeil();

      const td = selectedButton.closest("td");
      const tr = selectedButton.closest("tr");
      const colIndex = Array.from(tr.children).indexOf(td);

      if (colIndex >= 1 && colIndex <= 3) {
        veilColumnByIndex(colIndex);
        lastFadedColumn = colIndex;
      }

      selectedButton = null;

    }

    // helper to update column veil for a given button (keeps your veil logic DRY)
    function updateVeilForButton(btn) {
      if (!btn) return;
      clearColumnVeil();
      const td = btn.closest('td');
      if (!td) return;
      const tr = td.closest('tr');
      const colIndex = Array.from(tr.children).indexOf(td); // 0 = row label
      if (colIndex >= 1 && colIndex <= 3) {
        veilColumnByIndex(colIndex);
        lastFadedColumn = colIndex;
      }
    }


    // Slide button logic
    function handleSlide(removeFade) {
      document.querySelectorAll(".button.selected, .button-full.selected, .dark-faded")
        .forEach(el => el.classList.remove("selected", "dark-faded"));
      slideBtn.classList.add("selected");
      selectedButton = slideBtn;

      // Remove veil only if a column was previously green
      if (removeFade && toggledButton) {
        clearColumnVeil();
        
        toggledButton.classList.remove("faded");
        toggledButton = null;
        lastFadedColumn = null;
      }
    }

  </script>
</body>

</html>

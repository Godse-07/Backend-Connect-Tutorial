# Connect MongoDB with the React Vite

Although the creators of Vue build Vite, it supports various frameworks such as React, Svelte, SolidJS, Vanilla JavaScript, Lit, and Vue.

## Make FrontEnd

```bash
npm create vite@latest
```
```bash
cd <projectName>
```

```bash
npm install
```

## Create Backend
```bash
mkdir backend
cd backend
```

```bash
npm init -y
```
```bash
npm install express cors dotenv mongoose
```

## File Structures
<p align="center">
  <img src="https://github.com/user-attachments/assets/16b4129e-7e80-43c2-9984-02a6b7d68832" alt="Web_Photo_Editor" width="200" height="450" style="margin: 20px;"/>
  <br>
  <img src="https://github.com/user-attachments/assets/43ac0177-b257-4cb9-835c-d560046d8c7a" alt="Web_Photo_Editor" width="1000" height="450" style="margin: 20px;"/>
</p>

## In FrontEnd
```bash
##Install Axios (for making API requests to the backend):
npm install axios
```

## Make schema
```bash
import mongoose from "mongoose";

const textSchema = new mongoose.Schema({
  text: {
    type: String,
    required: true,
  },
  createdAt: {
    type: Date,
    default: Date.now,
  },
});

const Text = mongoose.model("Text", textSchema);
export default Text;

```

## make a server. js file

```bash
import mongoose from "mongoose";
import dotenv from "dotenv";
import cors from "cors";
import express from "express";
import Text from "./models/Text.js";

dotenv.config();

const app = express();
app.use(express.json());
app.use(cors());

// MongoDB Connection
mongoose.connect("mongodb://localhost:27017/demo_text", {
  useNewUrlParser: true,
  useUnifiedTopology: true,
})
  .then(() => console.log("Connected to MongoDB"))
  .catch((err) => console.error("Error connecting to MongoDB:", err));

// Route to save text
app.post("/api/save", async (req, res) => {
  try {
    const newText = new Text({ text: req.body.text });
    await newText.save();
    res.json({ message: "Text saved successfully!" });
  } catch (error) {
    res.status(500).json({ error: "Failed to save text" });
  }
});

// Route to fetch last saved text
app.get("/api/get-latest", async (req, res) => {
  try {
    const latestText = await Text.findOne().sort({ createdAt: -1 });
    res.json({ text: latestText ? latestText.text : "" });
  } catch (error) {
    res.status(500).json({ error: "Failed to retrieve text" });
  }
});

const PORT = process.env.PORT || 5000;
app.listen(PORT, () => console.log(`Server running on port ${PORT}`));


```

## Connect frontend with backend
```bash
import { useState, useEffect } from "react";
import axios from "axios";

function App() {
    const [inputText, setInputText] = useState("");
    const [outputText, setOutputText] = useState("");

    useEffect(() => {
        axios.get("http://localhost:5000/api/get-latest")
            .then(res => setOutputText(res.data.text))
            .catch(err => console.error(err));
    }, []);

    const handleSave = () => {
        if (!inputText.trim()) return;

        axios.post("http://localhost:5000/api/save", { text: inputText })
            .then(() => {
                setOutputText(inputText);
                setInputText("");
                console.log("Text saved successfully!");
            })
            .catch(err => console.error(err));
    };

    return (
        <div className="bg-slate-500 h-screen flex flex-col justify-center items-center text-white">
            <div className="mb-4 bg-slate-800 p-4 rounded-lg w-[45%] flex justify-around">
                <h1>Enter Text : </h1>
                <input 
                    type="text" 
                    name="text" 
                    className="bg-slate-500 pl-2"
                    placeholder="Enter text"
                    value={inputText}
                    onChange={(e) => setInputText(e.target.value)}
                />
                <button className="bg-slate-700 p-2 rounded-xl" onClick={handleSave}>
                    Press me
                </button>
            </div>
            <div className="bg-slate-800 p-4 rounded-lg w-[45%]">
                <h1>Output : </h1>
                <p>{outputText}</p>
            </div>
        </div>
    );
}

export default App;
```

## Contributing

Pull requests are welcome.

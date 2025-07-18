# P2P Energy Grid Monitor & Fault Impact

This project is a web-based application that simulates and analyzes the impact of various grid events on a Peer-to-Peer (P2P) energy-sharing network. It uses a trained PyTorch model to classify different types of power system faults and disturbances from 3-phase current waveform data. The frontend, built with Flask and JavaScript, allows users to visualize the P2P network, select a grid event, and see the analysis of its impact.

## ✨ Features

* **P2P Network Simulation**: Visually represents a microgrid with prosumers, consumers, and a central hub.
* **Grid Event Analysis**: Classifies various grid faults using a deep learning model.
* **Waveform Visualization**: Plots the 3-phase current waveforms for selected grid events using Plotly.js.
* **Dynamic Impact Assessment**: Shows the impact of a detected grid event on the P2P network, highlighting affected components.
* **Detailed Fault Information**: Provides descriptions and potential causes for each classified fault.
* **Interactive Frontend**: User-friendly interface to select different scenarios and view results.

## ⚙️ How It Works

The application operates as follows:

1.  **User Interaction**: The user selects a sender, a receiver, and a sample grid event from the dropdown menus on the web interface.
2.  **Backend Request**: A `POST` request is sent to the `/predict` endpoint of the Flask server with the selected data.
3.  **Data Loading**: The backend loads the corresponding waveform data (`.npy` file) for the selected grid event.
4.  **Preprocessing**: The waveform data is normalized using pre-calculated mean and standard deviation values.
5.  **Model Inference**: The preprocessed data is fed into a pre-trained 1D Convolutional Neural Network (ConvNet) model for fault classification.
6.  **Results Processing**: The model's prediction, confidence score, and associated fault information (description, causes) are retrieved.
7.  **Impact Analysis**: Based on the predicted fault type, an impact assessment on the P2P network is generated.
8.  **Response to Frontend**: The backend sends a JSON response to the frontend containing the prediction, fault details, impact analysis, and waveform data.
9.  **Frontend Update**: The JavaScript on the frontend parses the JSON response and dynamically updates the UI to display the results, including the waveform plot, fault information, and visual indicators on the P2P grid diagram.

## 💻 Technology Stack

* **Backend**: Flask, PyTorch, NumPy, Gunicorn
* **Frontend**: HTML, CSS, JavaScript, Tailwind CSS, Plotly.js
* **Model**: 1D Convolutional Neural Network (CNN) built with PyTorch

## 🚀 Setup and Installation

1.  **Clone the repository:**
    ```bash
    git clone [https://github.com/harenimuthu/power_grid_monitor.git](https://github.com/harenimuthu/power_grid_monitor.git)
    cd power_grid_monitor
    ```
2.  **Install dependencies:**
    ```bash
    pip install -r requirements.txt
    ```
3.  **Run the application:**
    ```bash
    python app.py
    ```
4.  Open your web browser and navigate to `http://127.0.0.1:5000`.

## 📂 File Descriptions

```
├── app.py                  # Main Flask application file
├── model_def.py            # PyTorch model definition (ConvNet)
├── requirements.txt        # Project dependencies
├── templates
│   └── index.html          # HTML template for the web interface
├── static
│   ├── css
│   │   └── styles.css      # Custom styles for the frontend
│   └── js
│       └── main.js         # JavaScript for frontend logic and interactivity
└── flask_artifacts
├── best_model.pth          # Saved PyTorch model state dictionary
├── fault_descriptions.json # Descriptions and causes for each fault type
├── labels.json             # Mapping from class index to label name
├── normalization_stats.npz # Mean and standard deviation for data normalization
├── sample_data/            # Directory for sample waveform data (.npy files)
└── samples_info.json       # Metadata for the sample data files


```

## 🧠 Model Architecture

The project uses a 1-D Convolutional Neural Network (ConvNet) to classify power system waveforms. The model architecture is defined in `model_def.py` and consists of:

* **Three 1D Convolutional Layers**: Each followed by Batch Normalization, a ReLU activation function, and Max Pooling. These layers extract features from the 3-phase waveform data.
* **A Classifier Head**: Composed of two fully connected (Linear) layers with a ReLU activation and a Dropout layer to prevent overfitting. This part of the model takes the flattened features from the convolutional layers and makes the final classification.

The model was trained on a dataset of 3-phase current waveforms representing various types of faults and transient events in a power grid.

## ↔️ API Endpoint

### `/predict`

* **Method**: `POST`
* **Description**: Analyzes a selected grid event and returns the impact assessment.
* **Request Body**:
    ```json
    {
        "sample_filename": "sample_00_internal_ispar_exciting_transformer_internal_faults_Class1.npy",
        "sender_peer": "prosumer-a",
        "receiver_peer": "consumer-home"
    }
    ```
* **Success Response (200 OK)**:
    ```json
    {
        "selected_sample": "sample_00_internal_ispar_exciting_transformer_internal_faults_Class1.npy",
        "sender_peer": "prosumer-a",
        "receiver_peer": "consumer-home",
        "predicted_label": "internal_ispar_exciting_transformer_internal_faults_Class1",
        "confidence": "99.99%",
        "true_label": "internal_ispar_exciting_transformer_internal_faults_Class1",
        "description": "Internal Fault (ISPAR Exciting Unit): Phase A to Ground (AG).",
        "causes": ["Insulation failure", "Bushing failure", "Tap changer fault"],
        "p2p_impact_details": {
            "event_type": "Internal Grid Equipment Fault",
            "highlight_keys": ["prosumer-a", "microgrid-hub"],
            "is_fault_condition": true,
            "p2p_disruption_message": "Grid fault! Transfer from Prosumer A likely disrupted. Fault in local microgrid regulation/transformation equipment. P2P transfers likely disrupted."
        },
        "waveform_data": [...]
    }
    ```
* **Error Responses**: `400 Bad Request`, `404 Not Found`, `500 Internal Server Error`

## 🎨 Frontend

The user interface is designed to be intuitive and informative. It is divided into two main sections:

* **Controls (Left Side)**:
    * **P2P Energy Transfer Setup**: Dropdowns to select the sender and receiver peers.
    * **Grid Event Selection**: A dropdown to choose from a list of pre-defined grid event samples.
    * **Analysis Button**: A button to trigger the impact analysis.
    * **P2P Microgrid Diagram**: A visual representation of the microgrid that dynamically changes color to indicate the grid status (normal or fault).

* **Analysis (Right Side)**:
    * **Grid Event Waveform**: A Plotly chart that displays the 3-phase current waveform of the selected event.
    * **P2P Impact Analysis**: A detailed report of the analysis, including the detected event, confidence level, description, potential causes, and the specific impact on the P2P energy transfer.

## 📖 How to Use

1.  **Select Peers**: Choose a "Sender Peer" and a "Receiver Peer" from the dropdown menus to simulate a P2P energy transfer.
2.  **Select a Grid Event**: Choose a sample grid event from the "Select Grid Event Sample" dropdown.
3.  **Analyze**: Click the "Analyze P2P Impact" button.
4.  **View Results**:
    * The **Grid Event Waveform** plot will display the 3-phase current waveform for the selected event.
    * The **P2P Impact Analysis** section will show the details of the detected event and its impact.
    * The **Illustrative P2P Microgrid** diagram will update with color indicators to show the status of the grid components. Red indicates a fault, and green indicates normal operation.

## 🔮 Future Improvements

* **Real-time Data Integration**: Connect the system to real-time data streams from power grid sensors.
* **Expanded Fault Library**: Train the model on a more extensive dataset to recognize a wider variety of grid events.
* **Advanced P2P Simulation**: Implement a more detailed simulation of P2P energy transactions, including pricing and energy flow calculations.
* **User Authentication**: Add user accounts to save and track analysis history.
* **Automated Control Actions**: Develop a system that can suggest or automate control actions (e.g., islanding the microgrid) in response to detected faults.

## 📜 License

This project is licensed under the MIT License. See the `LICENSE` file for more details.







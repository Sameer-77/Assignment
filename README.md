# Assignment

Create the main server file (server.js) and set up Express :

const express = require('express');
const bodyParser = require('body-parser');
const sequelize = require('./models');

const app = express();
app.use(bodyParser.json());

// Routes
app.use('/api/forms', require('./routes/forms'));

const PORT = process.env.PORT || 5000;
sequelize.sync().then(() => {
  app.listen(PORT, () => console.log(`Server running on port ${PORT}`));
});

Set up Sequelize and define the form model (models/Form.js):


const { Sequelize, DataTypes } = require('sequelize');
const sequelize = new Sequelize('database', 'username', 'password', {
  host: 'localhost',
  dialect: 'mysql'
});

const Form = sequelize.define('Form', {
  formType: {
    type: DataTypes.STRING,
    allowNull: false
  },
  name: {
    type: DataTypes.STRING,
    allowNull: false
  },
  countryCode: {
    type: DataTypes.STRING,
    allowNull: false
  },
  phoneNumber: {
    type: DataTypes.STRING,
    allowNull: false
  }
});

module.exports = Form;
module.exports.sequelize = sequelize;

Create API routes (routes/forms.js):

const express = require('express');
const router = express.Router();
const Form = require('../models/Form');

router.post('/', async (req, res) => {
  const { formType, name, countryCode, phoneNumber } = req.body;
  try {
    const newForm = await Form.create({ formType, name, countryCode, phoneNumber });
    res.json(newForm);
  } catch (err) {
    res.status(500).json({ error: err.message });
  }
});

router.get('/', async (req, res) => {
  try {
    const forms = await Form.findAll();
    res.json(forms);
  } catch (err) {
    res.status(500).json({ error: err.message });
  }
});

module.exports = router;


Setup the Client

// src/App.js
import React from 'react';
import { BrowserRouter as Router, Route, Switch, Link } from 'react-router-dom';
import FormA from './components/FormA';
import FormB from './components/FormB';
import Home from './components/Home';

function App() {
  return (
    <Router>
      <div className="App">
        <nav>
          <Link to="/">Home</Link>
          <Link to="/form-a">Form A</Link>
          <Link to="/form-b">Form B</Link>
        </nav>
        <Switch>
          <Route path="/" exact component={Home} />
          <Route path="/form-a" component={FormA} />
          <Route path="/form-b" component={FormB} />
        </Switch>
      </div>
    </Router>
  );
}

export default App;


// src/components/FormA.js
import React, { useState } from 'react';
import axios from 'axios';

function FormA() {
  const [name, setName] = useState('');
  const [countryCode, setCountryCode] = useState('');
  const [phoneNumber, setPhoneNumber] = useState('');
  const [error, setError] = useState('');

  const handleSubmit = async (e) => {
    e.preventDefault();
    if (!/^[A-Za-z]+$/.test(name)) {
      setError('Name must contain only alphabetic characters');
      return;
    }
    if (!/^\d+$/.test(phoneNumber)) {
      setError('Phone number must be numeric');
      return;
    }
    try {
      await axios.post('/api/forms', { formType: 'A', name, countryCode, phoneNumber });
      setError('');
    } catch (err) {
      setError('Failed to submit form');
    }
  };

  return (
    <div>
      <h1>Form A</h1>
      <form onSubmit={handleSubmit}>
        <div>
          <label>Name: </label>
          <input type="text" value={name} onChange={(e) => setName(e.target.value)} />
        </div>
        <div>
          <label>Country Code: </label>
          <select value={countryCode} onChange={(e) => setCountryCode(e.target.value)}>
            <option value="">Select Country Code</option>
            <option value="1">US (+1)</option>
            <option value="44">UK (+44)</option>
          </select>
        </div>
        <div>
          <label>Phone Number: </label>
          <input type="text" value={phoneNumber} onChange={(e) => setPhoneNumber(e.target.value)} />
        </div>
        <button type="submit">Submit</button>
        {error && <p style={{ color: 'red' }}>{error}</p>}
      </form>
    </div>
  );
}

export default FormA;


// src/components/FormB.js
import React, { useState } from 'react';
import axios from 'axios';

function FormB() {
  const [name, setName] = useState('');
  const [countryCode, setCountryCode] = useState('');
  const [phoneNumber, setPhoneNumber] = useState('');
  const [error, setError] = useState('');

  const handleSubmit = async (e) => {
    e.preventDefault();
    if (!/^[A-Za-z]+$/.test(name)) {
      setError('Name must contain only alphabetic characters');
      return;
    }
    if (!/^\d+$/.test(phoneNumber)) {
      setError('Phone number must be numeric');
      return;
    }
    try {
      await axios.post('/api/forms', { formType: 'B', name, countryCode, phoneNumber });
      setError('');
    } catch (err) {
      setError('Failed to submit form');
    }
  };

  return (
    <div>
      <h1>Form B</h1>
      <form onSubmit={handleSubmit}>
        <div>
          <label>Name: </label>
          <input type="text" value={name} onChange={(e) => setName(e.target.value)} />
        </div>
        <div>
          <label>Country Code: </label>
          <select value={countryCode} onChange={(e) => setCountryCode(e.target.value)}>
            <option value="">Select Country Code</option>
            <option value="1">US (+1)</option>
            <option value="44">UK (+44)</option>
          </select>// src/components/Home.js
import React from 'react';
import { Link } from 'react-router-dom';

function Home() {
  return (
    <div>
      <h1>Home</h1>
      <Link to="/form-a"><button>Form A</button></Link>
      <Link to="/form-b"><button>Form B</button></Link>
    </div>
  );
}

export default Home;

        </div>
        <div>
          <label>Phone Number: </label>
          <input type="text" value={phoneNumber} onChange={(e) => setPhoneNumber(e.target.value)} />
        </div>
        <button type="submit">Submit</button>
        {error && <p style={{ color: 'red' }}>{error}</p>}
      </form>
    </div>
  );
}

export default FormB;




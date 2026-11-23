const express = require('express');
const cors = require('cors');
const twilio = require('twilio');

const app = express();
app.use(cors());
app.use(express.urlencoded({ extended: false }));
app.use(express.json());

const PORT = process.env.PORT || 3000;

app.get('/', (req, res) => {
  res.send('Private PCO Gateway is running');
});

app.get('/token', (req, res) => {
  const identity = req.query.identity || 'pco_user';
  const AccessToken = twilio.jwt.AccessToken;
  const VoiceGrant = AccessToken.VoiceGrant;

  const token = new AccessToken(
    process.env.TWILIO_ACCOUNT_SID,
    process.env.TWILIO_API_KEY,
    process.env.TWILIO_API_SECRET,
    { identity }
  );

  const voiceGrant = new VoiceGrant({
    outgoingApplicationSid: process.env.TWILIO_TWIML_APP_SID,
    incomingAllow: true
  });

  token.addGrant(voiceGrant);
  res.json({ token: token.toJwt() });
});

app.post('/voice', (req, res) => {
  const VoiceResponse = twilio.twiml.VoiceResponse;
  const twiml = new VoiceResponse();
  const toNumber = req.body.To;

  if (toNumber) {
    const dial = twiml.dial({ callerId: process.env.TWILIO_CALLER_ID });
    if (/^[\d+\-\s()]+$/.test(toNumber)) {
      dial.number(toNumber);
    } else {
      dial.client(toNumber);
    }
  } else {
    twiml.say('Thank you for calling Private PCO.');
  }

  res.type('text/xml');
  res.send(twiml.toString());
});

app.listen(PORT, () => {
  console.log(`Server listening on port ${PORT}`);
});


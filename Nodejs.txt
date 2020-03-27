  'use strict';

var Alexa = require('alexa-sdk');
var APP_ID ='amzn1.ask.skill.d7ecf6a4-0b52-41e1-8727-716b72f2f673'; // TODO replace with your app ID (OPTIONAL).

exports.handler = function(event, context, callback) {
    var alexa = Alexa.handler(event, context);
    alexa.APP_ID = APP_ID;
    // To enable string internationalization (i18n) features, set a resources object.
    alexa.resources = languageStrings;
    alexa.registerHandlers(handlers);
    alexa.execute();
};

var handlers = {
    //Use LaunchRequest, instead of NewSession if you want to use the one-shot model
    // Alexa, ask [my-skill-invocation-name] to (do something)...
    'LaunchRequest': function () {
        this.attributes['speechOutput'] = languageStrings['en-US']['translation']['WELCOME_MESSAGE'];
        // If the user either does not reply to the welcome message or says something that is not
        // understood, they will be prompted again with this text.
        this.attributes['repromptSpeech'] = languageStrings['en-US']['translation']['WELCOME_REPROMT'];
        this.emit(':ask', this.attributes['speechOutput'], this.attributes['repromptSpeech'])
    },
    'GetAnswerIntent': function () {
		var num1Slot = this.event.request.intent.slots.NumOne;
		var operationSlot = this.event.request.intent.slots.Operation;
		var num2Slot = this.event.request.intent.slots.NumTwo;

        var cardTitle = languageStrings['en-US']['translation']['DISPLAY_CARD_TITLE'];
		
		if (!num1Slot || !num2Slot) {
			this.attributes['speechOutput'] = languageStrings['en-US']['translation']['INVALID_PROBLEM_MESSAGE'];
			this.attributes['repromptSpeech'] = languageStrings['en-US']['translation']['HELP_REPROMT'];
		} else if (!operationSlot){
			this.attributes['speechOutput'] = languageStrings['en-US']['translation']['INVALID_PROBLEM_MESSAGE'];
			this.attributes['repromptSpeech'] = languageStrings['en-US']['translation']['HELP_REPROMT'];
		} else {
			var num1 = Number(num1Slot.value);
			var num2 = Number(num2Slot.value);
			if(isNaN(num1) || isNaN(num2) || num1 == null || num2 == null || num1 == undefined || num2 == undefined){
				this.attributes['speechOutput'] = languageStrings['en-US']['translation']['INVALID_PROBLEM_MESSAGE'];
				this.attributes['repromptSpeech'] = languageStrings['en-US']['translation']['HELP_REPROMT'];
				this.emit(':tell', this.attributes['speechOutput'], this.attributes['repromptSpeech']);
				return;
			}
			var operation = operationSlot.value.toLowerCase();
			var answer;
			switch(operation){
				case "plus":
					answer = num1 + num2;
					break;
				case "minus":
					answer = num1 - num2;
					break;
				case "times":
					answer = num1 * num2;
					break;
				case "divided by":
					answer = num1 / num2;
					break;
				default:
					this.attributes['speechOutput'] = languageStrings['en-US']['translation']['INVALID_PROBLEM_MESSAGE'];
					this.attributes['repromptSpeech'] = languageStrings['en-US']['translation']['HELP_REPROMT'];
					this.emit(':tell', this.attributes['speechOutput'], this.attributes['repromptSpeech']);
					return;
			}
			this.attributes['speechOutput'] = num1 + " " + operation + " " + num2 + " equals " + answer;
			this.attributes['repromptSpeech'] = languageStrings['en-US']['translation']['HELP_REPROMT'];
			this.emit(':tell', this.attributes['speechOutput'], this.attributes['repromptSpeech']);
		}
    },
    'AMAZON.HelpIntent': function () {
        this.attributes['speechOutput'] = languageStrings['en-US']['translation']['HELP_MESSAGE'];
        this.attributes['repromptSpeech'] = languageStrings['en-US']['translation']['HELP_REPROMT'];
        this.emit(':ask', this.attributes['speechOutput'], this.attributes['repromptSpeech']);
    },
    'AMAZON.RepeatIntent': function () {
        this.emit(':ask', this.attributes['speechOutput'], this.attributes['repromptSpeech']);
    },
    'AMAZON.StopIntent': function () {
        this.emit('SessionEndedRequest');
    },
    'AMAZON.CancelIntent': function () {
        this.emit('SessionEndedRequest');
    },
    'SessionEndedRequest':function () {
        this.emit(':tell', languageStrings['en-US']['translation']['STOP_MESSAGE']);
    }
};

var languageStrings = {
    "en-US": {
        "translation": {
            "SKILL_NAME" : "Simple Calculator",
            "WELCOME_MESSAGE": "Welcome to the Simple Calculator. Ask me a simple arithmetic question.",
            "WELCOME_REPROMT": "For instructions on what you can say, please say help me.",
            "HELP_MESSAGE": "Ask me a simple arithmetic question like what is 2 plus 2, or you can say exit",
            "HELP_REPROMT": "Ask me a simple arithmetic question like what is 2 plus 2, or you can say exit",
            "STOP_MESSAGE": "Goodbye!",
            "INVALID_PROBLEM_MESSAGE": "I couldn\'t understand the question. I can do basic arithmetic like addition, subtraction, multiplication, and division. Please ask me a math problem like what is 2 plus 2."
        }
    }
};

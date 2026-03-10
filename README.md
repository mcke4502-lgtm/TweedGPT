import os
import gradio as gr

# Try to import OpenAI
try:
    import openai
    OPENAI_AVAILABLE = True
except:
    OPENAI_AVAILABLE = False

# Cheat sheet fallback
apush_facts = {
    "when was the civil war": "yoooooooo brrrrrrroooooooo 1861–1865",
    "who wrote the declaration of independence": "yoooooooo brrrrrrroooooooo Thomas Jefferson",
    "what was the missouri compromise": "yoooooooo brrrrrrroooooooo 1820, Missouri slave state, Maine free state",
    "what is the emancipation proclamation": "yoooooooo brrrrrrroooooooo 1863, issued by Abraham Lincoln",
    "when was the constitution ratified": "yoooooooo brrrrrrroooooooo 1788",
    "alexander hamilton": "yoooooooo brrrrrrroooooooo First Secretary of the Treasury",
    "thomas jefferson": "yoooooooo brrrrrrroooooooo Third President of the US",
    "john adams": "yoooooooo brrrrrrroooooooo Second President of the US",
    "monroe doctrine": "yoooooooo brrrrrrroooooooo 1823",
    "industrial revolution": "yoooooooo brrrrrrroooooooo Early-mid 1800s in the US",
    "manifest destiny": "yoooooooo brrrrrrroooooooo 19th century expansion westward",
    "seneca falls convention": "yoooooooo brrrrrrroooooooo 1848, women's rights convention",
    "reconstruction era": "yoooooooo brrrrrrroooooooo 1865–1877",
    "civil rights act": "yoooooooo brrrrrrroooooooo 1964",
    "voting rights act": "yoooooooo brrrrrrroooooooo 1965",
    # …add more APUSH facts as you like
}

def tweed_gpt(question):
    q_lower = question.lower().strip()
    
    # Try OpenAI GPT first
    api_key = os.environ.get("OPENAI_API_KEY")
    if OPENAI_AVAILABLE and api_key:
        openai.api_key = api_key
        try:
            prompt = f"You are TweedGPT, an AP US History helper. Always start replies with 'yoooooooo brrrrrrroooooooo'. Answer clearly: {question}"
            response = openai.ChatCompletion.create(
                model="gpt-3.5-turbo",
                messages=[{"role": "user", "content": prompt}],
                max_tokens=300
            )
            return response['choices'][0]['message']['content']
        except openai.error.RateLimitError:
            # fallback to cheat sheet
            pass
        except openai.error.AuthenticationError:
            return "yoooooooo brrrrrrroooooooo OpenAI API key invalid!"
        except Exception as e:
            return f"yoooooooo brrrrrrroooooooo OpenAI error ({type(e).__name__}): {str(e)}"

    # Fallback to cheat sheet
    return apush_facts.get(q_lower, "yoooooooo brrrrrrroooooooo Sorry, I don't know that one!")

# Gradio web interface
iface = gr.Interface(
    fn=tweed_gpt,
    inputs=gr.Textbox(label="Ask TweedGPT your APUSH question"),
    outputs=gr.Textbox(label="TweedGPT says"),
    title="TweedGPT - APUSH Helper",
    description="yoooooooo brrrrrrroooooooo Ask me any APUSH question! Uses OpenAI GPT if available, otherwise cheat sheet."
)

iface.launch(server_name="0.0.0.0", server_port=int(os.environ.get("PORT", 7860)))

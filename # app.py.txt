# app.py
import streamlit as st
import plotly.graph_objects as go

# --- Questions ---
power_qs = [
    "P1: The greatest danger in politics is giving institutions too much power.",
    "P2: The greatest danger in politics is failing to use power to address serious problems.",
    "P3: Even well-intentioned governments usually drift toward abuse of authority.",
    "P4: A capable government with enough authority can solve most major social problems.",
    "P5: I worry more about tyranny than disorder.",
    "P6: I worry more about disorder than tyranny.",
    "P7: In major crises, governments should be willing to set aside normal limits on their power.",
    "P8: It’s better to live with some unsolved problems than to risk concentrating too much authority."
]

anthro_qs = [
    "A1: People will always find ways to exploit any system.",
    "A2: Most harmful behavior is caused by bad social conditions rather than bad character.",
    "A3: Power- and status-seeking are permanent features of human psychology.",
    "A4: In a well-designed society, most people would behave well most of the time.",
    "A5: Over the long run, humanity is becoming morally better.",
    "A6: Human nature does not change much across history."
]

knowledge_qs = [
    "K1: We can deliberately design better social systems using reason and evidence.",
    "K2: Most important institutions work because of historical trial and error, not conscious design.",
    "K3: If a system produces bad outcomes, it should probably be redesigned from scratch.",
    "K4: Traditions often exist for reasons we don’t fully understand.",
    "K5: Complex systems should be optimized, not merely preserved.",
    "K6: Top-down reforms usually fail because they ignore local knowledge."
]

risk_qs = [
    "R1: The current system is so unjust that major risks are justified to change it.",
    "R2: It’s better to tolerate known problems than to risk catastrophic unintended consequences.",
    "R3: Big problems require big, fast solutions.",
    "R4: Most large-scale reforms do more harm than good."
]

rules_qs = [
    "C1: Some actions are wrong even if they would lead to better outcomes.",
    "C2: If breaking rules produces much better results, it’s usually justified.",
    "C3: Rights and due process should not be overridden, even in emergencies.",
    "C4: What matters most is reducing suffering, not following abstract rules."
]

# --- Archetype Descriptions ---
descriptions = {
    "Classical Liberal / Institutional Constrainer": """You see human nature as flawed and power as dangerous. You trust evolved institutions more than top-down design. Your instinct is to preserve and refine constraints.
Closest traditions: Classical liberalism, Burkean liberalism, Hayekian institutionalism
Closest thinkers: Hume, Madison, Hayek, Burke
Potential blind spots: Can be overly cautious and slow to address injustices.""",
    "Libertarian Constrainer": """You minimize coercion and favor voluntary systems. Power is your main concern. 
Closest traditions: Libertarianism, classical liberalism
Closest thinkers: Nozick, Rothbard, early Spencer
Potential blind spots: May underestimate coordination or public goods challenges.""",
    "Hybrid / Rare Constrainer": """A rare mix of perfectible human nature and constraint instincts. Highly idealistic but cautious.
Potential blind spots: Difficult to map in real politics; may experience internal tension.""",
    "Utopian Anarchist / Radical Constrainer": """You see humans as highly perfectible and power as inherently corrupting. You favor decentralized voluntary systems.
Closest traditions: Anarchism, radical decentralism
Closest thinkers: Proudhon, early Godwin
Potential blind spots: May underestimate coordination challenges.""",
    "Technocratic Manager": """You see power as a tool to solve problems, and society as improvable through rational management.
Closest traditions: Technocracy, managerial liberalism
Closest thinkers: Bentham, modern policy technocrats
Potential blind spots: May over-rely on experts or ignore local knowledge.""",
    "Authoritarian Realist": """You see humans as flawed and power as necessary to impose order. Stability is prioritized over liberty.
Closest traditions: Authoritarian conservatism, reactionary statism
Closest thinkers: Hobbes, Carl Schmitt
Potential blind spots: May overemphasize order and justify abuses.""",
    "Progressive Reformer": """You believe humans are somewhat perfectible and power can improve society. Reform is morally urgent.
Closest traditions: Social democracy, progressive liberalism
Closest thinkers: Rawls (partly), Dewey
Potential blind spots: May underestimate limits of knowledge and incentives.""",
    "Utopian / Socialist Reformer": """You believe humans are highly perfectible and power can transform society. Radical change is justified.
Closest traditions: Socialism, revolutionary liberalism
Closest thinkers: Marx, Godwin
Potential blind spots: Risk of authoritarian drift or unintended consequences."""
}

# --- Helper Function ---
def mean(scores):
    return sum(scores)/len(scores)

# --- Streamlit App ---
st.title("Political Instinct Quiz with Radar Chart")
st.write("Answer each question on a scale from 1 (Strongly Disagree) to 7 (Strongly Agree).")

# Collect responses
power_scores = [st.slider(q, 1, 7, 4) for q in power_qs]
anthro_scores = [st.slider(q, 1, 7, 4) for q in anthro_qs]
knowledge_scores = [st.slider(q, 1, 7, 4) for q in knowledge_qs]
risk_scores = [st.slider(q, 1, 7, 4) for q in risk_qs]
rules_scores = [st.slider(q, 1, 7, 4) for q in rules_qs]

if st.button("Submit"):
    # Averages
    power_avg = mean(power_scores)
    anthro_avg = mean(anthro_scores)
    knowledge_avg = mean(knowledge_scores)
    risk_avg = mean(risk_scores)
    rules_avg = mean(rules_scores)

    # Determine types
    power_type = "Constrainer" if power_avg < 4 else "Power-User"
    anthro_type = "Tragic" if anthro_avg >= 4 else "Perfectible"
    knowledge_type = "Rationalist" if knowledge_avg >= 4 else "Emergent"

    # Archetype mapping
    if power_type == "Constrainer":
        if anthro_type == "Tragic":
            archetype = "Classical Liberal / Institutional Constrainer" if knowledge_type == "Emergent" else "Libertarian Constrainer"
        else:
            archetype = "Hybrid / Rare Constrainer" if knowledge_type == "Emergent" else "Utopian Anarchist / Radical Constrainer"
    else:
        if anthro_type == "Tragic":
            archetype = "Technocratic Manager" if knowledge_type == "Emergent" else "Authoritarian Realist"
        else:
            archetype = "Progressive Reformer" if knowledge_type == "Emergent" else "Utopian / Socialist Reformer"

    flavor = ("Radical" if risk_avg >= 4 else "Cautious") + " / " + ("Rule-Focused" if rules_avg >=4 else "Outcome-Focused")

    # Display result
    st.subheader("Your Political Instinct Quiz Result")
    st.write(f"**Archetype:** {archetype}")
    st.write(f"**Flavor:** {flavor}")
    st.write(descriptions.get(archetype, "No description available."))
    st.write("**Your category scores:**")
    st.write(f"Power: {power_avg:.2f}, Anthropology: {anthro_avg:.2f}, Knowledge: {knowledge_avg:.2f}, Risk: {risk_avg:.2f}, Procedure: {rules_avg:.2f}")

    # --- Radar Chart ---
    categories = ['Power', 'Anthropology', 'Knowledge', 'Risk', 'Procedure']
    values = [power_avg, anthro_avg, knowledge_avg, risk_avg, rules_avg]
    values += values[:1]  # Close the loop

    fig = go.Figure(
        data=[go.Scatterpolar(r=values, theta=categories + [categories[0]], fill='toself', name='Profile')],
        layout=go.Layout(
            polar=dict(radialaxis=dict(visible=True, range=[1,7])),
            showlegend=False,
            title="Political Profile Radar Chart"
        )
    )
    st.plotly_chart(fig)

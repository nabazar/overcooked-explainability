<div>
<h1 align="center">
  <br>
  <a href="https://hpai.bsc.es/"> 
      <img src="Images/Logos/hpai_logo_blue.png" alt="FIB, UPC Logo" width="20%">
      <img src="Images/Logos/logo-upc.png" alt="FIB, UPC Logo" width="53%">
      <img src="Images/Logos/bsc-logo.png" alt="FIB, UPC Logo" width="11%">

</a>
<h1>
    Testing Reinforcement Learning Explainability Methods in a Multi-agent Cooperative Environment 🧑‍🍳🤖
</h1>
<h2 align="center">
  Marc Domènech i Vila, Dmitry Gnatyshak, Adrián Tormos and Sergio Alvarez-Napagao
</h2>
<h3 align="center">
  High Performance Artificial Intelligence research group (HPAI), BSC-UPC
</h3>
<br></br>
</div>

Even though with each passing day the AI gains popularity thanks to its successful application in many domains, the truth is that it also receives a lot of criticism. In particular, people ask themselves if its decisions are well-informed and if they can rely on its decisions. The answers to these questions become crucial in cooperative environments to be understandable to humans and can cooperate with them. In this work, we will apply an approach for explainability based on the creation of a Policy Graph (PG) that represents the agent’s behaviour. This work has two main contributions: the first is a way to measure the similarity between the explanations and the agent’s behaviour, by building another agent that follows a policy based on the explainability method and comparing the behaviour of both agents. The second manages to explain an RL agent in a multi-agent cooperative environment.

# 🥘 Introduction

In this work, we have used [PantheonRL](https://github.com/Stanford-ILIAD/PantheonRL) package for training and testing an agent in the [Overcooked-AI](https://github.com/HumanCompatibleAI/overcooked_ai) environment. Overcooked-AI is a benchmark environment for fully cooperative human-AI task performance, based on the wildly popular video game [Overcooked](http://www.ghosttowngames.com/overcooked). The goal of the game is to deliver soups as fast as possible. Each soup requires placing up to 3 ingredients in a pot, waiting for the soup to cook, and then having an agent pick up the soup and delivering it. The agents should split up tasks on the fly and coordinate effectively in order to achieve high reward. The environment has the following reward function: 3 points if the agent places an onion in a pot or if takes a dish, and 5 points if it takes a soup. Here in this work, we have worked with five different layouts: *simple*, *unident_s*, *random0*, *random1*, *random3*.

# ✅ Installation

---

In this version of the repo, we are going to build a docker image. This docker image has installed Ubuntu and the repo is installed using Miniconda. Run the following command lines to get started.

```bash
docker build -t overcooked_img .
docker run -it overcooked_img
```

Once we ran the docker container, then we run the following commands:

```bash
conda activate overcooked_env
```

# 🤖 Training our RL Agent

---

When installing the repository, we can see some pre-trained models. We can use any of them or train one on our own. To do so, we can execute one of the following command lines.

```bash
cd Code/Training

# Train an agent with ID=0 in layout simple for 1000000 timesteps
bash train.sh 0 simple 1000000
```

Once the training had been finished, we will be able to see the following trained agents in [`rl_models`](rl_models) folder:

- **Ego Agent:** The ego-agent is considered the main agent in the environment. From the perspective of the ego agent, the environment functions like a regular gym environment.
- **Alt Agents:** The alt-agents are the partner agents that are embedded in the environment. If multiple are listed, the environment randomly samples one of them to be the partner at the start of each episode.

If we want to personalize more your agent, you could use the following command line and add all the configurations that you want:

```bash
cd PantheonRL

python3 trainer.py OvercookedMultiEnv-v0 PPO PPO --env-config '{"layout_name":"simple"}' --ego-save models/ego0 --alt-save models/alt0
```

# 🧪 Test our RL Agent

---

We can test our agents with the following command line:

```bash
cd Code/Testing
# The first parameter is the ID of the agent
# The second parameter is the layout
bash test.sh 0 simple
```

Once the testing had been finished, we will be able to see the mean episode reward and other useful information.

If we want to personalize more your agent, you could use the following command line and add all the configurations that you want:

```bash
cd PantheonRL

python3 tester.py OvercookedMultiEnv-v0 PPO PPO --env-config '{"layout_name":"simple"}' --ego-load models/ego --alt-load models/alt
```

Also we can test the agent in a web interface:

```bash
cd Code/Testing
# The first parameter is the ID of the agents
# The second parameter is the layout
bash test_GUI.sh 0 simple
```

> ## 👍 Trained Models
> 
> When cloning the repository you will see that in [`rl_models`](rl_models) folder, there are already trained models. Here I attach a brief summary of each one. Each of them have been trained five for 1M total timesteps and with an episode length of 400 steps.
> 
> | Layout    | Mean Episode Reward | Standard Deviation |
> | --------- | ------------------- | ------------------ |
> | simple    | 387.87              | 25.33              |
> | unident_s | 757.71              | 53.03              |
> | random0   | 395.01              | 54.43              |
> | random1   | 266.01              | 48.11              |
> | random3   | 62.5                | 5.00               |

# 🏛 Repo Structure Overview

---

Here we can see a brief summary of the repo structure.

## 🐍 Main scripts

- [`run_experiment.py`](run_experiment.py): Reproduces a single experiment. With this script, we can build and test a Policy Graph agent .
- [`run_all_experiments.py`](run_all_experiments.py): Reproduces all the experiments. With this script, we can build and test a set of Policy Graph agents.
- [`ask_xai_questions.py`](ask_xai_questions.py): It opens a menu where we can ask for explanations for a given PG agent.
- [`get_subgraph.py`](get_subgraph.py): Takes a MDP agent and saves a subgraph.

## 🧑🏼‍💻 Code Folder

- [`Training`](Code/Training): Code related with the training of the agents.
  - [`train.sh`](Code/Training/train.sh): Script that trains an Ego and Alt agent using PPO in a particular layout.
- [`Testing`](Code/Testing): Code related with the testing of the agents.
  - [`test.sh`](Code/Testing/test.sh): Script that tests a trained Ego agent in a particular layout.
  - [`test_GUI.sh`](Code/Testing/test_GUI.sh): Script that tests a trained Ego agent using GUI.
- [`Explainability`](Code/Explainability): Code related with the agent explainability.
  - [`PolicyGraph.py`](Code/Explainability/PolicyGraph.py): Abstract class with all the general computations of the Policy Graph algorithm.
  - [`PartialPolicyGraph.py`](Code/Explainability/PartialPolicyGraph.py): Class that implements Partial Policy Graph algorithm.
  - [`CompletePolicyGraph.py`](Code/Explainability/CompletePolicyGraph.py): Class that implements Complete Policy Graph algorithm.
  - [`Discretizers`](Code/Explainability/Discretizers): All the discretizer implementations.
- [`Utils`](Code/Utils): Code related with useul tools.
  - [`utils.py`](Code/Utils/utils.py): Code with usefool tools.
- [`Experiment.py`](Code/Experiment.py): Code to reproduce different experiements.
- [`join_csv_results.py`](Code/join_csv_results.py): Script that joins 2 .csv files into one csv. Used to join Partial and Complete agent results.

## 🦾 PantheonRL Folder

This folder has a lot of files. Here I mention those files that I think are more interesting.

- Train/Test
  - [`tester.py`](Code/PantheonRL/tester.py): Code for testing the Agents.
  - [`trainer.py`](Code/PantheonRL/trainer.py): Code for training an Agents in a particular layout.
  - [`bctrainer.py`](Code/PantheonRL/bctrainer.py): Code for training a BC Agent.
  - [`app.py`](Code/PantheonRL/overcookedgym/overcooked-flask/app.py): Executes a Flask App where we can see the agents playing in a GUI.
- Examples
  - [`OvercookedAdaptPartnerInstructions.md`](Code/PantheonRL/overcookedgym/OvercookedAdaptPartnerInstructions.md): Training with terminal examples.
  - [`OvercookedFlaskWebAppInstructions.md`](Code/PantheonRL/overcookedgym/OvercookedFlaskWebAppInstructions.md): Web app examples.
  - [`overcookedtraining.py`](Code/PantheonRL/examples/overcookedtraining.py): Example of how to train an Agent with Python.
- Environment
  - [`overcooked_env.py`](Code/PantheonRL/overcookedgym/human_aware_rl/overcooked_ai/overcooked_ai_py/mdp/overcooked_env.py): Overcooked environment.
  - [`overcooked_mdp.py`](Code/PantheonRL/overcookedgym/human_aware_rl/overcooked_ai/overcooked_ai_py/mdp/overcooked_mdp.py): Overcooked state in class `OvercookedState`. Player state in class `PlayerState`.
- Policy Graph Extraction
  - [`overcooked.py`](Code/PantheonRL/overcookedgym/overcooked.py): Implements the `OvercookedMultiEnv(SimultaneousEnv)` class. I have modified the `multi_step()` in order to also return the observation with the shape we want.

# 📗 Glossary

---

- **Episode:** It refers to a Game. One game consists in taking 400 actions, this means that an agent will take 400 actions over the game.
- **Epoch:** It refers to a set of Episodes.

# 🔬Contributing

---

1. Fork the project (https://github.com/yourname/yourproject/fork)
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -am 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Create a new Pull Request

# ➕ More Information

---

For more information about the project, see the following documentation:

- ✅ [Paper](Documents/Testing_Reinforcement_Learning_Explainability_Methods_in_a_Multi_agent_Cooperative_Environment_IOSPress.pdf) presented at [24th International Conference of the Catalan Association for Artificial Intelligence (CCIA 2022)](https://ccia2022.cat/) and published at [IOS Press Ebooks](https://ebooks.iospress.nl/volumearticle/61264)

- ✅ [Bachelor's Thesis](Documents/Bachelor's-thesis.pdf) presented at [FIB - UPC](https://www.fib.upc.edu/)

- 🏆This project has been awarded with an accessit to the [Best Bachelor's Thesis 2022 by ACIA](https://www.acia.cat/ca/prize-to-the-best-bsc-or-msc-thesis/). Attached documents: [[PDF]](Documents/ACIA_Accessit_Best_Bachelors_Thesis.pdf)

- 🏆This project has been awarded with the [CCIA 2022 Best Paper Award](%5Bhttps://ccia2022.cat/%5D(https://ccia2022.cat/)). Attached documents: [[PDF]](Documents/CCIA_22_Best_Paper_Award.pdf)

```tex
@article{domenech_pg_2022,
   abstract = {The adoption of algorithms based on Artificial Intelligence (AI) has been rapidly increasing during the last years. However, some aspects of AI techniques are under heavy scrutiny. For instance, in many cases, it is not clear whether the decisions of an algorithm are well-informed and reliable. Having an answer to these concerns is crucial in many domains, such as those in were humans and intelligent agents must cooperate in a shared environment. In this paper, we introduce an application of an explainability method based on the creation of a Policy Graph (PG) based on discrete predicates that represent and explain a trained agent's behaviour in a multi-agent cooperative environment. We also present a method to measure the similarity between the explanations obtained and the agent's behaviour, by building an agent with a policy based on the PG and comparing the behaviour of the two agents.},
   author = {Marc Domènech i Vila and Dmitry Gnatyshak and Adrián Tormos and Sergio Alvarez-Napagao},
   doi = {10.3233/FAIA220358},
   keywords = {Cooperative Environments,Explainable AI,Multi-agent Reinforcement Learning,Policy Graphs,Reinforcement Learning},
   month = {10},
   pages = {355-364},
   publisher = {IOS Press},
   title = {Testing Reinforcement Learning Explainability Methods in a Multi-Agent Cooperative Environment},
   url = {https://ebooks.iospress.nl/doi/10.3233/FAIA220358},
   year = {2022},
}
```

# 🙋‍♂️ Authors

---

### Bachelor's Thesis student

- **Marc Domènech** - [MarcDV1999](https://github.com/MarcDV1999)

### Bachelor's Thesis supervisor

- **Sergio Álvarez-Napagao** - ([Sergio Álvarez-Napagao](mailto:sergio.alvarez@bsc.es))

### Bachelor's Thesis co-supervisor

- **Dmitry Gnatyshak** - ([Dmitry Gnatyshak](mailto:dmitry.gnatyshak@bsc.es))
- **Adrián Tormos** - ([Adrián Tormos](mailto:adrian.tormos@bsc.es))

# 🎓 License

---

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details

## Further Issues and questions ❓

---

If you have issues or questions, don't hesitate to contact [MarcDV1999](https://github.com/MarcDV1999) at [marcdomenechvila@gmail.com](mailto:marcdomenechvila@gmail.com).

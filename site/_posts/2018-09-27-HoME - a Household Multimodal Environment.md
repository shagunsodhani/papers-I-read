---
layout: post
title: HoME - a Household Multimodal Environment
comments: True
excerpt: 
tags: ['2017', 'Multi Modal', 'NIPS 2017', 'NIPS Workskop', 'Virtual Embodiment', AI, NIPS]
---

## Introduction

* Environment for learning using modalities like vision, audio, semantics, physics and interaction with objects and other agents.

* [Link to the paper](https://arxiv.org/abs/1711.11017)

## Motivation

* Humans learn by interacting with their surroundings (environment).

* Similarly training an agent in an interactive multi-model environment (virtual embodiment) could be useful for a learning agent.


## Characteristics

* Open-source and Open-AI gym compatible

* Built on top of 45000 3D house layouts from SUNCG dataset.

* Provides both 3D visual and audio recording.

* Semantic image segmentation and langauge description of objects.

## Components

* Rendering Engine

    * Implemented using Panda 3D game engine.
    
    * Renders RGB+depth scenes based on textures, multi-source lightings and shadows.

* Acoustic Engine

    * Implemented using EVERT

    * Supports multiple microphones, sound sources, sound absorption based on material, atmospheric conditions etc.

* Semantics Engine
    
    * Provides a short textual description for each object, along with information like color, category, material size, location etc.

* Physics Engine
    
    * Implemented using Bullet3 Engine

    * Supports physical interaction, external forces like gravity and position and velocity information for multiple agents.

## Potential Applications

* Visual Question Answering

* Conversational Agents

* Training an agent to follow instructions

* Multi-agent communication
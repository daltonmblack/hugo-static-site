---
title: "Hello Quantum Computing! (Amazon Braket)"
description: "My first time using quantum computing using Amazon's newly released product, Braket."
date: "2020-10-27"
categories:
  - "tutorial"
tags:
  - "quantum"
  - "braket"
  - "aws"
  - "blog"
---

Intro
-----

When I first learned about the existence of quantum computing, I did not imagine that by 2020, it would be accessible for regular developers to try for themselves.

In August of this year, Amazon made quantum computing officially available to the general public with its product, Braket. In this brief article, I share some of my notes, resources, and thoughts from completing a "Hello, World!" for Amazon Braket.

Resources
---------

Here are resources I found helpful during my introduction to Braket:

* [Tutorial](https://aws.amazon.com/blogs/aws/amazon-braket-go-hands-on-with-quantum-computing/) by Jeff Barr, Chief Evangelist for AWS
* Amazon Braket [Developer Guide](https://docs.aws.amazon.com/braket/latest/developerguide/braket-developer-guide.pdf)
* Amazon Braket [Pricing](https://aws.amazon.com/braket/pricing/)
* Python quantum simulator on GitHub: [aws/amazon-braket-default-simulator-python](https://github.com/aws/amazon-braket-default-simulator-python)

Notes
-----

* This tutorial introduced me to ML “notebooks”.
  * Notebooks allow the programmer/data-scientist to interlace code with text, images, output, etc.
  * Notebooks are a core part of the Python ML ecosystem. They are found at https://jupyter.org/.
* Here is a screen-capture of me running Braket’s “Hello World”:
![Amazon Braket Hello World Screen Capture](/images/amazon-braket-notebook-test-compressed.gif "Amazon Braket Hello World Screen Capture")
* The “Hello World” example is pre-written along with various other introduction labs to quantum computing. These labs are only accessible once you “open your notebook” which is running on an Amazon SageMaker Studio Notebook (minimum size ml.t3.medium, which is [$0.0582/hour](https://aws.amazon.com/sagemaker/pricing/)).
![Jupyter Quantum Labs Examples](/images/jupyter_quantum_labs.png "Jupyter Quantum Labs Examples")
* Quantum programs are able to be run on simulators as well as quantum hardware
  * Simulators
    * Local simulator (up to 25 qubits)
      * Runs on local hardware and is free to use
    * Managed simulator (up to 34 qubits)
      * Managed by AWS and costs $4.50/hour
  * Hardware ([pricing](https://aws.amazon.com/braket/pricing/))
    * D-Wave
    * IonQ
    * Rigetti

Conclusion
----------

Understanding the complexity of quantum mechanics may be incredibly difficult, but experimenting with basic quantum programming is actually really simple!

* The path of least resistance is to run simulations in Amazon’s environment
  * The base AWS instance cost to run the development environment is: [$0.0582/hour](https://aws.amazon.com/sagemaker/pricing/)
  * Running simulations using the local hardware option within the environment is free
* It is possible to set up the development environment entirely locally via Amazon’s GitHub project [aws/amazon-braket-default-simulator-python](https://github.com/aws/amazon-braket-default-simulator-python)

_If you found this article useful, I encourage you to follow my personal journey as I learn AWS technologies. Don't hesitate to reach out via any of my socials. I'd love to chat about any comments/questions/ideas you have._

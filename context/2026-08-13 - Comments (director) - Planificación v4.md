1. I would add a control experiment with fine-tuning on the same dataset, but without synthetic degradations, to separately evaluate the effect of the adaptation itself, rather than the mere fact of additional training.

2. It is also worth reconsidering the cost metric: for a pretrained vs. fine-tuned model of the same architecture, it is not very appropriate to compare inference time, which in principle will barely change.

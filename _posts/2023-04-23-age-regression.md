---
layout: post
title: Age prediction with Pytorch image recognition
---
# Age regression with image recognition (Pytorch)
---
<div class="post-date">
    <span>{{page.date | date: "%Y.%m.%d" }}</span>
</div>
<div class="intro">
There isn’t that much out there about doing regression based on image recognition or at least a lot less than I’d expect, so I wanted to do a post on how you can implement in a simple way with Pytorch. On Kaggle there’s a decent dataset with image samples for people aged in the age range of 0-100. I figured this was a good enough dataset to apply regression on, based on image recognition. I’ll be going through the process of implementing this and discuss the relevant parts of the code. There is a link to the code here. Note that ehe focus here is just getting to a working base model, and not a super optimized model. It's best read a bit of knowledge on Pytorch or Tensorflow.
</div>

<ul class="tags">
    <li>Python</li>
    <li>Pytorch</li>
    <li>Regression model</li>
</ul>

## Inspecting the dataset
The dataset has been divided into a train/test split, where the train split has 185632 samples, and the test split 47568. This is definitely a good amount of samples to train with to get a working model. Note, that normally you’d of course also have a validation split to validate your training, but for simplicity I’ll use the train/test split as is. In terms of quality all images are 125x125 so it is a bit on the lower quality side. When inspecting some samples some people look younger/older than the label says so there might be some error in the labels, but most of it seems correct (enough). See some samples in the image below:

![dataset samples]({{site.url}}/assets/images/age-regression/dataset_samples.png)


## Setting up the model
For the model I’m picking a Resnet that’s pre-trained on Imagenet. It’s a model that’s already included with Torch, thus easy to just import and use. I’ve used it a lot training other types of models and problems, and it has always worked well in my personal experience. For this experiment I will be running the training for Resnet18 and Resnet50. Picking a lower Resnet could get better performance for more simple datasets as it overfits less and is also faster to train, so it’s worth trying it out.

```
model = resnet50(
    weights=ResNet50_Weights.IMAGENET1K_V1
)
```
On top of the model I add a fully connected layer that sets up the model to outputs one continuous number, the regression output.
```
# Modification for regression -------
num_features = model.fc.in_features

# only train the last layer
for param in model.parameters():
    param.requires_grad = False

# by default the model is set to predict 1000 (Imagenet) classes
# set it to 1 as we're just gonna predict 1 continuous value
model.fc = torch.nn.Linear(num_features, 1).to(device)
```
## Setting up the dataset and dataloader
First we’ll set up a Torch dataset which will load the samples from the data folder. The most important thing to know is to inherit `torch.utils.data.Dataset` in your dataset class and implement the `__getitem__` and `__len__` function, which is used by the data loader later on. In my case the `__getitem__` returns a tuple containing the tensor of the image and tensor of the label, and the `__len__` just returns the amount of samples. See my dataset implementation below.
```
class AgeDataset(Dataset):
    
    def __init__(self, dataset_dir: Path):
        self.dataset_dir = dataset_dir
        self.ages = list((dataset_dir).glob("*"))
        self.samples = []
        
        for age_dir in self.ages:
            for sample in age_dir.glob('*.jpg'):
                self.samples.append(sample)
                
    def load_image(self, sample):
        image = Image.open(sample).convert("RGB")
        np_image = to_tensor(image)
        
        return np_image

    def __getitem__(self, idx):
        sample = self.load_image(self.samples[idx])
        label = torch.Tensor([
            int(self.samples[idx].parent.name)
        ])  # age labels need to be wrapped dimension for the MSEloss function input
        
        return sample, label
        
    def __len__(self):
        return len(self.samples)
```

The dataloader `torch.utils.data.DataLoader` will take an instance of this dataset and is directly called in the training loop which will return batches from the dataset it was initiated with. In initializing the dataloader you can define things like the batch_size, shuffling the dataset and more. This is easily to initiated like so:

`train_dataloader = DataLoader(train_dataset, batch_size=25, drop_last=True, shuffle=True)`

## Training
In the function below you can see the training function which is mostly a pretty standard Pytorch training loop here. First I define the essentials for a model training which is the loss function and the optimizer. The loss function to use for regression, or at least the most common, is the mean squared error(MSE), which is just the prediction value minus the target value squared. This will be collected for each batch from which the mean will be taken from. As the optimizer I use Adam optimizer which should work alright for this case.

The outer loop is the epoch loop, the inner loop is the dataloader loop which loops over every batch in the training dataset. Each iteration basically calculates the MSE loss and optimizes the model weights.

```
def train(loader, epochs):

    # the basic params you'd need to configure for training
    loss_function = MSELoss()

    learning_rate = 1e-3
    optimizer = Adam(
        model.fc.parameters(),
        lr=learning_rate
    )

    # epoch loop
    for epoch_idx in range(epochs):
        print(f"start training epoch {epoch_idx}")
        total_loss = 0

        # batch loop
        for batch_idx, batch in enumerate(loader):

            images = batch[0].to(device)
            labels = batch[1].to(device)

            optimizer.zero_grad()

            outputs = model(images)

            loss = loss_function(outputs, labels)
            loss.backward()

            optimizer.step()

            total_loss += loss.item()

            if batch_idx % 250 == 0 and batch_idx != 0:
                print(f"Trained {batch_idx}/{len(loader)} batches! | batch loss: {loss}")

        batch_loss = total_loss / len(loader)
        print(f"average batch loss for epoch {epoch_idx}: {batch_loss}!")

        total_loss = 0
```

## Evaluating the model
For the evaluating the model I've used 1000 of the test sample test. Overall the results are not great, but not terrible. It's clear that the resnet50 performs a bit better. I have trained it for 5 epochs which isn't that long but it's enough to show it works which was my goal here.

|model|Mean squared error|Root mean squared error|
|:---|:---|:---|
|ResNet18|165.23|12.85|
|ResNet50|135.81|11.65|

### Plotting some predictions
Below you can see some samples that the resnet50 model has performed better on. It comes relatively close and at least outputs sensible predictions which was my goal here. The biggest takeaway from inspecting the model is that kids are often predicted much older than they are. On the most right you see a girl that's 14 but has been predicted as 19 plus.

![better predictions]({{site.url}}/assets/images/age-regression/better_samples.png)

I have also plotted the not so good results here below. The first one is off by 25 years but it's also a bit harder to predict due to the grayscale to be fair. Here you see a baby that's predicted as 20 (lol) and the last kid as almost 30, which shows further how bad it performs on younglings. In the third picture you see the actor James McAvoy who is falsely labeled as 57(who is actually 44 now and also younger in this image as well) which shows there's definitely label errors in the dataset.

![better predictions]({{site.url}}/assets/images/age-regression/worse_samples.png)

## Conclusions
To optimize the model a step that definitely has to taken is to do some data cleanup. From inspecting the dataset I get the sense that the percentage of errors is significant enough to where it's affecting the model performance. My initial guess is that this one of the big things that will raise the model performance, as good data is just vital to a good model. In this case especially for the younglings where the performance is bad. From experience doing data cleanups can sometimes give the model quite a boost in performance. Other steps that could be taken after this is: more training iterations, adding some data augmentation, training more layers, experimenting with different learning rates, trying different Resnets or other networks, among other things.

<hr/>
I hope this read was helped or was interesting, and for questions you can always contact me via my Github.

## Table of contents
#### 1. Data
#### 2. Processing data
#### 3. Dataloader
#### 4. Training
#### 5. Evaluation

### Data
Data was collected from 20th of June 2021 to 20th of June 2022, with over 42,000 titles in 5 categories from VnExpress. All the code to get data in `data\get_data.py`.

```python
def get_titles(category, start_date, end_date):
	
	delta = timedelta(days=1)
	
	titles = []
	
	while start_date <= end_date:
	
	date = start_date.strftime("%d/%m/%Y")
	
	unix_time = int(datetime.strptime(date, '%d/%m/%Y').strftime("%s"))
	
	r = requests.get(f'https://vnexpress.net/category/day?cateid={category}&fromdate={unix_time}&todate={unix_time}&allcate={category}')
	
	soup = BeautifulSoup(r.text, 'html.parser')
	
	raw_titles = soup.find_all('h3','title-news')
	
	for title in raw_titles:
	
	titles.append(title.find('a')['title'])
	
	start_date += delta
	
	return titles
```

```
    Title	                                                Category
0	Bão Dianmu vào Thừa Thiên Huế - Quảng Ngãi	        Thời sự
1	Nadal bị đánh giá thấp hơn Djokovic và Alcaraz...	Thể thao
2	Cháy 10 cửa hàng ở Hà Nội	                        Thời sự
3	Belarus nói quân đội Ukraine 'bất mãn với Tổng...	Thế giới
4	Liverpool thua khi Salah hỏng phạt đền	            Thể thao
```

### Processing data
I use [underthesea](https://github.com/undertheseanlp/underthesea) for word segment and [PhoBERT](https://huggingface.co/PhoBERT) for tokenization.

```python
from underthesea import word_tokenize

def preprocess(text):

    """Conditional preprocessing on our text unique to our task."""

    # Lower

    text = text.lower()
    # Remove words in paranthesis

    text = re.sub(r'\([^)]*\)', '', text)

    # Spacing and filters

    text = re.sub(r"([-;;.,!?<=>])", r" \1 ", text)

    text = re.sub(r'[^A-Za-z0-9wáàảãạăắằẳẵặâấầẩẫậéèẻẽẹêếềểễệóòỏõọôốồổỗộơớờởỡợíìỉĩịúùủũụưứừửữựýỳỷỹỵđ]+',' ',text)# remove non alphanumeric chars

    text = re.sub(' +', ' ', text)  # remove multiple spaces

    text = text.strip()

    return  word_tokenize(text, format='text')
```

```python
text = "Thủ tướng Nhật cảnh báo 'Đông Á có thể là Ukraine tiếp theo'"
preprocess(text)
'thủ_tướng nhật cảnh_báo đông_á có_thể là ukraine tiếp_theo'
```

```python
phobert = AutoModel.from_pretrained("vinai/phobert-base", return_dict = False)
tokenizer = AutoTokenizer.from_pretrained("vinai/phobert-base", return_dict = False)

text = 'thủ_tướng nhật cảnh_báo đông_á có_thể là ukraine tiếp_theo'
tokenizer.(text)

{'input_ids': [0, 4739, 21697, 1223, 35352, 6992, 62, 8, 1881, 22055, 3403, 1512, 2], 'token_type_ids': [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0], 'attention_mask': [1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1]}

tokenizer.decode(tokenizer(text)['input_ids'])
<s> thủ_tướng nhật cảnh_báo đông_á có_thể là ukraine tiếp_theo </s>
```

### Label Encoder with one hot encoding
```python
class LabelEncoder(object):

    """Label encoder for tag labels."""

    def __init__(self, class_to_index={}):
        self.class_to_index = class_to_index
        self.index_to_class = {v: k for k, v in self.class_to_index.items()}
        self.classes = list(self.class_to_index.keys())

    def __len__(self):

        return len(self.class_to_index)

    def __str__(self):

        return f"<LabelEncoder(num_classes={len(self)})>"

    def fit(self, y):
        classes = np.unique(y)
        for i, class_ in enumerate(classes):
            self.class_to_index[class_] = i
        self.index_to_class = {v: k for k, v in self.class_to_index.items()}
        self.classes = list(self.class_to_index.keys())
        
        return self

    def encode(self, y):
        y_one_hot = np.zeros((len(y), len(self.class_to_index)), dtype=int)
        for i, item in enumerate(y):
            y_one_hot[i][self.class_to_index[item]] = 1
        return y_one_hot

    def decode(self, y):

        classes = []
        for i, item in enumerate(y):
            index = np.where(item == 1)[0][0]
            classes.append(self.index_to_class[index])

        return classes

    def save(self, fp):
        with open(fp, "w") as fp:
            contents = {'class_to_index': self.class_to_index}
            json.dump(contents, fp, indent=4, sort_keys=False,ensure_ascii=False)
```

### Dataloader
Create data loader for input data. See more on [pytorch docs](https://pytorch.org/docs/stable/data.html).

```python
class DataLoader(torch.utils.data.Dataset):

    def __init__(self, ids, masks, targets):
        self.ids = ids
        self.masks = masks
        self.targets = targets

    def __len__(self):

        return len(self.targets)

    def __str__(self):

        return f"<Dataset(N={len(self)})>"

    def __getitem__(self, index):

        ids = torch.tensor(self.ids[index], dtype=torch.long)
        masks = torch.tensor(self.masks[index], dtype=torch.long)
        targets = torch.FloatTensor(self.targets[index])
        return ids, masks, targets


    def create_dataloader(self, batch_size, shuffle=False, drop_last=False):

        return torch.utils.data.DataLoader(
            dataset=self,
            batch_size=batch_size,
            shuffle=shuffle,
            drop_last=drop_last,
            pin_memory=False)
```


### Training
Initialize `Trainer` class

```python
class Trainer(object):
    def __init__(self, model, device, loss_fn=None, optimizer=None, scheduler=None):

        self.model = model
        self.device = device
        self.loss_fn = loss_fn
        self.optimizer = optimizer
        self.scheduler = scheduler

    def train_step(self, dataloader):

        self.model.train()
        loss = 0.0
  
        # Iterate over train batches
        for i, batch in enumerate(dataloader):

            batch = [item.to(self.device) for item in batch]  # Set device
            inputs, targets = batch[:-1], batch[-1]
            self.optimizer.zero_grad()  # Reset gradients
            z = self.model(inputs)  # Forward pass
            J = self.loss_fn(z, targets)  # Define loss
            J.backward()  # Backward pass
            self.optimizer.step()  # Update weights
            loss += (J.detach().item() - loss) / (i + 1)
        return loss

    def train(self, num_epochs, patience, train_dataloader, val_dataloader):

        best_val_loss = np.inf
        for epoch in range(num_epochs):
            train_loss = self.train_step(dataloader=train_dataloader)
            val_loss, _, _ = self.eval_step(dataloader=val_dataloader)
            self.scheduler.step(val_loss)

            if val_loss < best_val_loss:
                best_val_loss = val_loss
                best_model = self.model
                _patience = patience  # reset _patience
            else:
                _patience -= 1
            if not _patience:  # 0
                print("Stopping early!")
                break

            print(
                f"Epoch: {epoch+1} | "
                f"train_loss: {train_loss:.5f}, "
                f"val_loss: {val_loss:.5f}, "
                f"lr: {self.optimizer.param_groups[0]['lr']:.2E}, "
                f"_patience: {_patience}"
            )

        return best_model

    def eval_step(self, dataloader):

        """Validation or test step."""

        # Set model to eval mode

        self.model.eval()
        loss = 0.0
        y_trues, y_probs = [], []

        with torch.inference_mode():
            for i, batch in enumerate(dataloader):

                batch = [item.to(self.device) for item in batch]  # Set device
                inputs, y_true = batch[:-1], batch[-1]
                z = self.model(inputs)  # Forward pass
                J = self.loss_fn(z, y_true).item()
                loss += (J - loss) / (i + 1)
                y_prob = F.softmax(z).cpu().numpy()
                y_probs.extend(y_prob)
                y_trues.extend(y_true.cpu().numpy())

        return loss, np.vstack(y_trues), np.vstack(y_probs)
```

Initialize `PhoBert` class

```python
class PhoBert(nn.Module):

    def __init__(self, phobert, dropout_p, embedding_dim, num_classes):

        super(PhoBert, self).__init__()

        self.phobert = phobert

        self.dropout = torch.nn.Dropout(dropout_p)

        self.fc1 = torch.nn.Linear(embedding_dim, num_classes)

    def forward(self, inputs):

        ids, masks = inputs

        seq, pool = self.phobert(input_ids=ids, attention_mask=masks)

        z = self.dropout(pool)

        z = self.fc1(z)

        return z
```

Trainning was instance with `Trainer` and `PhoBERT` custom class.

```python
model = PhoBert(
    phobert=phobert, dropout_p=dropout_p,
    embedding_dim=embedding_dim, num_classes=num_classes)
model = model.to(device)
trainer = Trainer(
    model=model, device=device, loss_fn=loss_fn, 
    optimizer=optimizer, scheduler=scheduler)
trainer.train(num_epochs, patience, train_dataloader, val_dataloader)

Epoch: 1 | train_loss: 0.00003, val_loss: 0.00002, lr: 1.00E-04, _patience: 10
Epoch: 2 | train_loss: 0.00002, val_loss: 0.00002, lr: 1.00E-04, _patience: 9
Epoch: 3 | train_loss: 0.00001, val_loss: 0.00002, lr: 1.00E-04, _patience: 10
Epoch: 4 | train_loss: 0.00001, val_loss: 0.00002, lr: 1.00E-04, _patience: 10
Epoch: 5 | train_loss: 0.00001, val_loss: 0.00002, lr: 1.00E-04, _patience: 9
Epoch: 6 | train_loss: 0.00000, val_loss: 0.00002, lr: 1.00E-04, _patience: 8
Epoch: 7 | train_loss: 0.00000, val_loss: 0.00002, lr: 1.00E-04, _patience: 7
Epoch: 8 | train_loss: 0.00000, val_loss: 0.00002, lr: 1.00E-04, _patience: 6
Epoch: 9 | train_loss: 0.00000, val_loss: 0.00002, lr: 1.00E-04, _patience: 5
Epoch: 10 | train_loss: 0.00000, val_loss: 0.00002, lr: 1.00E-05, _patience: 4
Epoch: 11 | train_loss: 0.00000, val_loss: 0.00002, lr: 1.00E-05, _patience: 3
Epoch: 12 | train_loss: 0.00000, val_loss: 0.00002, lr: 1.00E-05, _patience: 2
Epoch: 13 | train_loss: 0.00000, val_loss: 0.00002, lr: 1.00E-05, _patience: 1
Stopping early!

```

### Evaluation
The evaluation result store in `model\performance.json` file

Overall, the model is able to classify the data with precision of around 0.92%. Thời sự category has the lowest precision, around 84%.

```
{
  "overall": {
    "precision": 0.9254264897691822,
    "recall": 0.924386814560225,
    "f1": 0.9246180762437886,
    "num_samples": 6401.0
  },
  "class": {
    "Kinh doanh": {
      "precision": 0.9292035398230089,
      "recall": 0.9264705882352942,
      "f1": 0.9278350515463918,
      "num_samples": 1360.0
    },
    "Sức khỏe": {
      "precision": 0.9145850120870266,
      "recall": 0.8937007874015748,
      "f1": 0.9040223018717642,
      "num_samples": 1270.0
    },
    "Thế giới": {
      "precision": 0.9380222841225627,
      "recall": 0.9064602960969045,
      "f1": 0.9219712525667351,
      "num_samples": 1486.0
    },
    "Thể thao": {
      "precision": 0.9801071155317521,
      "recall": 0.9831158864159631,
      "f1": 0.9816091954022989,
      "num_samples": 1303.0
    },
    "Thời sự": {
      "precision": 0.8426013195098964,
      "recall": 0.9103869653767821,
      "f1": 0.8751835535976504,
      "num_samples": 982.0
    }
  }
}
```

### Saving model
 The model saved in `model\model.pt`, loading this model for prediction later.

### Deployment
This part is not revelant to the course, so I will not going to detail here.

## Acknowledgement

- [Made with ML](https://madewithml.com/)

- [Full stack deep learning](https://fullstackdeeplearning.com/spring2021/lecture-4/) 

- Pham Dinh Khanh [blog](https://phamdinhkhanh.github.io/2020/06/04/PhoBERT_Fairseq.html)






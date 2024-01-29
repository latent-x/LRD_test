# Source Code	

`main.py` serves as the entrance of our framework, and there are three main packages. 

### Structure

- `helpers\`
  - `BaseReader.py`: read dataset csv into DataFrame and append necessary information (e.g. interaction history)
  - `BaseRunner.py`: control the training and evaluation process of a model
  - `...`: customize helpers with specific functions
- `models\`
  - `BaseModel.py`: basic model classes and dataset classes, with some common functions of a model
  - `...`: customize models inherited from classes in *BaseModel*
- `utils\`
  - `layers.py`: common modules for model definition (e.g. attention)
  - `utils.py`: some utils functions
- `main.py`: main entrance, connect all the modules
- `exp.py`: repeat experiments in *run.sh* and save averaged results to csv 
- `run.sh`: running commands for each model



### Define a New Model

Generally we define a new class inheriting *GeneralModel* (a subclass of *BaseModel*), as well as the inner class *Dataset*, and implement the following functions:

```python
class NewModel(GeneralModel):
    reader = 'BaseReader'  # assign a reader class, BaseReader by default
    runner = 'BaseRunner'  # assign a runner class, BaseRunner by default

    def _define_params(self):
        # define parameters in the model

    def forward(self, feed_dict):
        # generate prediction (ranking score according to tensors in feed_dict)
        item_id = feed_dict['item_id']  # [batch_size, -1]
        user_id = feed_dict['user_id']  # [batch_size]
        prediction = (...)
        out_dict = {'prediction': prediction.view(feed_dict['batch_size'], -1)}
        return out_dict
    
    class Dataset(GeneralModel.Dataset):
        # construct feed_dict for a single instance (called by __getitem__)
        # will be collated to a integrated feed dict for each batch
        def _get_feed_dict(self, index):
            feed_dict = super()._get_feed_dict(index)
            (...)
            return feed_dict
```


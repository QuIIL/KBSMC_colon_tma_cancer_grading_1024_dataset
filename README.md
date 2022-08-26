# KBSMC colon tma cancer grading dataset
Description of the KBSMC_colon_tma_cancer_grading dataset

Google drive [[link]](https://drive.google.com/drive/folders/1RarodOuIVdyy2loJNBWI3qIE0zePulug?usp=sharing)

The tissue image and annotation are provided by Kangbuk Samsung Hospital, Seoul, South Korea. \
The annotation has been delineated by two pathologist: Kim, Kyungeun and Song, Boram.
The patch images are generated at 40x of size 1024x1024 pixels.

# Dataset structure

├── KBSMC_colon_tma_cancer_grading_1024 \
│ ├── tma01 \
│      │  ├── 100100_19493_**0**.jpg  \
│      │  ├── 100221_64120_**3**.jpg \
│      │  ├── 100383_42563_**2**.jpg \
│      │  ├── 100443_52718_**2**.jpg \
│      │  ├── 100634_14705_**1**.jpg \
│      │  ├── ... \
│ ├── tma02 \
│ ├── tma03 \
│ ├── tma04 \
│ ├── tma05 \
│ ├── tma06 \
│ ├── wsi01 \
│ ├── wsi02 \
│ └── wsi03 

### Notes:
Class label is the last digit in image name (bolden), for example 100100_19493_**0**.jpg belongs to benign class.

## Dataset detail
| Status | Benign (0) | WD (1) | MD (2) | PD (3) |
|--------|------------|--------|--------|--------|
| Train  | 773        | 1866   | 2997   | 1391   |
| Val    | 374        | 264    | 370    | 234    |
| Test   | 453        | 192    | 738    | 205    |

## Simple way to load the dataset
Check out the dataset.py

```python
def prepare_colon_tma_data(data_root_dir='./KBSMC_colon_tma_cancer_grading_1024'):
    """ List all the images and their labels
    """
    def load_data_info(pathname):
        file_list = glob.glob(pathname)
        label_list = [int(file_path.split('_')[-1].split('.')[0]) for file_path in file_list]
        print(Counter(label_list))
        return list(zip(file_list, label_list))

    set_tma01 = load_data_info('%s/tma01/*.jpg' % data_root_dir)
    set_tma02 = load_data_info('%s/tma02/*.jpg' % data_root_dir)
    set_tma03 = load_data_info('%s/tma03/*.jpg' % data_root_dir)
    set_tma04 = load_data_info('%s/tma04/*.jpg' % data_root_dir)
    set_tma05 = load_data_info('%s/tma05/*.jpg' % data_root_dir)
    set_tma06 = load_data_info('%s/tma06/*.jpg' % data_root_dir)
    set_wsi01 = load_data_info('%s/wsi01/*.jpg' % data_root_dir)  # benign exclusively
    set_wsi02 = load_data_info('%s/wsi02/*.jpg' % data_root_dir)  # benign exclusively
    set_wsi03 = load_data_info('%s/wsi03/*.jpg' % data_root_dir)  # benign exclusively

    train_set = set_tma01 + set_tma02 + set_tma03 + set_tma05 + set_wsi01
    valid_set = set_tma06 + set_wsi03
    test_set = set_tma04 + set_wsi02

    return train_set, valid_set, test_set



class DatasetSerial(data.Dataset):
    """get image by index
    """
    def __init__(self, pair_list, img_transform=None, target_transform=None, two_crop=False):
        self.pair_list = pair_list

        self.img_transform = img_transform
        self.target_transform = target_transform
        self.num = self.__len__()

    def __getitem__(self, index):
        """
        Args:
            index (int): index
        Returns:
            tuple: (image, index, ...)
        """
        path, target = self.pair_list[index]
        image = pil_loader(path)

        # # image
        if self.img_transform is not None:
            img = self.img_transform(image)
        else:
            img = image

        return img, target

```

Check out some works that have used this dataset:
<br />
```
@article{le2021joint,
  title={Joint categorical and ordinal learning for cancer grading in pathology images},
  author={Le Vuong, Trinh Thi and Kim, Kyungeun and Song, Boram and Kwak, Jin Tae},
  journal={Medical image analysis},
  pages={102206},
  year={2021},
  publisher={Elsevier}
}

@article{vuong2021multi,
  title={Multi-scale binary pattern encoding network for cancer classification in pathology images},
  author={Vuong, Trinh TL and Song, Boram and Kim, Kyungeun and Cho, Yong M and Kwak, Jin T},
  journal={IEEE Journal of Biomedical and Health Informatics},
  volume={26},
  number={3},
  pages={1152--1163},
  year={2021},
  publisher={IEEE}
}
```
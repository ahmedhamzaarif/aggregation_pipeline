- Accumulator doesn't overwrite, it keeps adding
- Accumulators Eg: avg, sum, push
### How many users are active?
```
[
  {
    $match: {
      isActive: true,
    },
  },
  {
    $count: "activeUsers",
  }
]
```

### What is the average age of all users?
```
[
  {
    $group: {
      _id: null,
      averageAge: {
        $avg: '$age'
      }
    }
  }
]
```

### List the 5 most common fruits among users
```
[
  {
    $group: {
      _id: '$favoriteFruit',
      count: {
        $sum: 1
      }
    }
  },
  {
    $sort: {
      count: -1
    }
  },
  {
    $limit: 5
  }
]
```

### Find the total number of male & females
```
[
  {
    $group: {
      _id: '$gender',
      count: {
        $sum: 1
      }
    }
  }
]
```

### Which country has the highest number of registered users?
```
[
  {
    $group: {
      _id: "$company.location.country",
      userCount: {
        $sum: 1
      }
    }
  },
  {
    $sort: {
      userCount: -1
    }
  },
  {
    $limit: 1
  }
]
```

### List all unique eye colors present in the collection
```
[
  {
    $group: {
      _id: '$eyeColor',
    }
  }
]
```

### What is the average number of tags per user?
```
// method 1
[
  {
    $unwind: '$tags'
  },
  {
    $group: {
      _id: '$_id',
      tagsCount: {
        $sum: 1
      }
    }
  },
  {
    $group: {
      _id: null,
      avgTagsCount: {
        $avg: '$tagsCount'
      }
    }
  }
]

// method 2
[
  {
    $addFields: {
      tagsCount: {
        $size: {$ifNull: ['$tags', []]}
      }
    }
  },
  {
    $group: {
      _id: null,
      avgTagsCount: {
        $avg: '$tagsCount',
      }
    }
  }
]
```

### How many users have 'enim' as one of their tags?
```
[
  {
    $match: {tags: 'enim'}
  },
  {
    $count: 'userWithEnimTag'
  }
]
```

### What are the names & age of users who are inactive and have 'velit' as a tag
```
[
  {
    $match: {
      isActive: false, tags: 'velit'
    }
  }, 
  {
    $project: {
      name: 1,
      age: 1
    }
  }
]
```

### How many users have a phone number starting with '+1 (940)'
```
[
  {
    $match: {
      "company.phone": /^\+1 \(940\)/
    }
  },
  {
    $count: 'users'
  }
]
```

### Who has registered most recently?
```
[
  {
    $sort: {
      registered: -1
    }
  },
  {
    $limit: 5
  },
  {
    $project: {
      name: 1,
      registered: 1,
      favoriteFruit: 1,
    }
  }
]
```

### Categorize users by their favorite fruit
```
[
  {
    $group: {
      _id: '$favoriteFruit',
      users: {
        $push: '$name'
      }
    }
  }
]
```

### How many users have 'ad' as the second tag in their list of tags?
```
[
  {
    $match: {
      'tags.1': 'ad'
    }
  },
  {
    $count: 'count' 
  }
]
```

### Find users who have both 'enim' & 'id' as their tags
```
[
  {
    $match: {
      tags: {
        $all: ['enim', 'id']
    	}
    }
  },
  {
    $count: 'count' 
  }
] 
``` 

### List all the companies in the USA with their corresponding user count
```
[
  {
    $match: {
      "company.location.country" : "USA"
    }
  },
  {
    $group: {
      _id: '$company.title',
      userCount: {
        $sum: 1
      }
    }
  },
  {
    $sort: {
      userCount: -1
    }
  }
]
```

### 
```
[
  {
    $lookup: {
      from: 'authors',
      localField: 'author_id',
      foreignField: '_id',
      as: 'author_details'
    }
  },
  {
    $addFields: {
      author_details: {
        // $first: '$author_details'
        $arrayElemAt: ['$author_details', 0]
      }
    }
  }
]
```
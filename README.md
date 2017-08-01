# edc-search

Add a slug field to models using the model mixin `SearchSlugModelMixin`. Specify the fields and/or properties to include in the `slug` in `search_slug_fields`:


    class TestModel(SearchSlugModelMixin, models.Model):
    
        search_slug_fields = ['f1', 'f2', 'f3']
    
        f1 = models.CharField(max_length=25, null=True)
        f2 = models.DateTimeField(null=True)
        f3 = models.IntegerField(null=True)
        f4 = models.CharField(max_length=25, null=True)
    
Fields in the `search_slug_fields` are converted to string in the slug:

    >>> obj = TestModel.objects.create(f1='run rabbit run!', f2=get_utcnow(), f3=12345)
    >>> obj.slug
    'run-rabbit-run!|2017-06-02 19:08:32.163520+00:00|12345'
    
Fields not listed are not included:

    >>> obj = TestModel.objects.create(f1='slug me', f4='don\'t slug me')
    >>> obj.slug
    'slug-me||'

`Null` fields are converted to `''`:

    >>> obj = TestModel.objects.create()
    >>> obj.slug
    '||'

You can use dotted syntax:

    class TestModel(SearchSlugModelMixin, models.Model):
    
        search_slug_fields = ['f1', 'name.first', 'name.last']
    
        f1 = models.CharField(max_length=25, null=True)
        
        def name(self):
            return FullName(first='Gore', last='vidal')

    >>> obj = TestModel.objects.create()
    >>> obj.slug
    '|Gore|Vidal'

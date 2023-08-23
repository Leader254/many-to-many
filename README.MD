```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            //many-to-many
            var someArtists = new Artist[]{
                new Artist { ArtistId = 1, FirstName = "Pablo", LastName="Picasso" },
                new Artist { ArtistId = 2, FirstName = "Dee", LastName="Bell" },
                new Artist { ArtistId = 3, FirstName ="Katharine", LastName="Kuharic" } };
            modelBuilder.Entity<Artist>().HasData(someArtists);
            var someCovers = new Cover[]{
                new Cover { CoverId = 1, DesignIdea="How about a left hand in the dark?", DigitalOnly=false },
                new Cover { CoverId = 2, DesignIdea= "Should we put a clock?", DigitalOnly=true },
                new Cover { CoverId = 3, DesignIdea="A big ear in the clouds?", DigitalOnly = false }};
            modelBuilder.Entity<Cover>().HasData(someCovers);
        }
```

## Add an existing artist to an existing cover

```csharp
        public void ConnectExistingArtistAndCovers()
        {
            var ArtistA = _context.Artists.Find(1);
            var ArtistB = _context.Artists.Find(2);

            var CoverA = _context.Covers.Find(1);
            CoverA.Artists.Add(ArtistA);
            CoverA.Artists.Add(ArtistB);
            _context.SaveChanges();
        }
```

## new cover to an existing artist

```csharp
        public void NewCoverToAnArtist()
        {
            var ArtistA = _context.Artists.Find(1);
            var cover = new Cover { DesignIdea = "Learn EFCore" };
            cover.Artists.Add(ArtistA);
            _context.Covers.Add(cover);
            _context.SaveChanges();
        }
```

## new cover to a new Artist

```csharp
        public void NewCoverNewArtist()
        {
            var artist = new Artist { FirstName = "Julie", LastName = "Lerman" };
            var cover = new Cover { DesignIdea = "Best Practices" };
            artist.Covers.Add(cover); //Associate the cover with the artist
            //cover.Artists.Add(artist);
            _context.Artists.Add(artist); //Add the artist to the db context
            _context.SaveChanges();
        }
```

## retrieve an artist with their covers

```csharp
        public void RetrieveArtistWithCovers()
        {
            var artistWithCovers = _context.Artists
                .Include(a => a.Covers)
                .FirstOrDefault(a => a.ArtistId == 1);
            if (artistWithCovers != null)
            {
                Console.WriteLine($"Artist: {artistWithCovers.FirstName} has {artistWithCovers.Covers.Count()}");

                foreach (var cover in artistWithCovers.Covers)
                {
                    Console.WriteLine($"Cover Design Idea: {cover.DesignIdea}");
                }
            }
        }
```

## retrieve a cover with its artists

```csharp
        public void RetrieveACoverAndItsArtists()
        {
            var coverWithArtists = _context.Covers
                .Include(a => a.Artists)
                .FirstOrDefault(c => c.CoverId == 1);
            if (coverWithArtists != null)
            {
                Console.WriteLine($"Covers: {coverWithArtists.DesignIdea} has {coverWithArtists.Artists.Count()}");
                foreach (var item in coverWithArtists.Artists)
                {
                    Console.WriteLine($"Artists: {item.FirstName} {item.LastName}");
                }
            }
            else
            {
                Console.WriteLine("Cover not found");
            }
        }
```

## unassign an artist from a cover

```csharp
        public void UnassignArtistFromCover()
        {
            var coverWithThatArtist = _context.Covers
                .Include(a => a.Artists.Where(a => a.ArtistId == 2))
                .FirstOrDefault(c => c.CoverId == 1);
            coverWithThatArtist.Artists.RemoveAt(0);
            //_context.Artists.Remove(coverWithThatArtist.Artists[0]);
            _context.ChangeTracker.DetectChanges();
            _context.SaveChanges();
        }
```

## reassign a cover to another artist

```csharp
        public void ReassignACover()
        {
            var coverWithArtist = _context.Covers
                .Include(a => a.Artists.Where(a => a.ArtistId == 4))
                .FirstOrDefault(c => c.CoverId == 5);

            coverWithArtist.Artists.RemoveAt(0);
            var anotherArtist = _context.Artists.Find(3);
            coverWithArtist.Artists.Add(anotherArtist);
            _context.SaveChanges();
        }
```

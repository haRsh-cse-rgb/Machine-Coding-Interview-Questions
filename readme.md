import React, { useEffect, useState } from 'react'
import InfiniteScroll from 'react-infinite-scroll-component';

const itemsPerPage = 10;

const App = () => {

  const [photo, setphoto] = useState([]);
  const [loading, setloading] = useState(false);
  const [hasmore, sethasmore] = useState(true);
  const [page, setpage] = useState(1);

  useEffect(() => {
    fetchdata()
  }, []);

  const fetchdata = async () => {
    setloading(true);
    try {
      const result = await fetch(`https://picsum.photos/v2/list?page=${page}&limit=${itemsPerPage}`);
      const newphotos = await result.json();

      if (newphotos.length === 0) {
        setloading(false);
        sethasmore(false);
      }
      else {

        setphoto(prev => [...prev, ...newphotos]);
        setpage(prev => prev + 1);
      }
    } catch (error) {
      console.log(error)
    } finally {
      setloading(false);
    }
  }
  return (
    <div style={{ width: "800px", margin: "auto" }}>
      {loading && photo.length === 0 && <h2>Loading Photos</h2>
      }
      <InfiniteScroll
        dataLength={photo.length}
        next={fetchdata}
        hasMore={hasmore}
        loader={<h4>Loading more photos...</h4>}
        endMessage={<p style={{ textAlign: 'center' }}><b>No more photos to load.</b></p>}>

        {photo.map((i) => (
          <div key={i.id}
            style={{
              display: 'flex',
              alignItems: 'center',
              margin: '10px',
              gap: '10px'
            }}

          >

            <img src={i.download_url} alt={`Photo by ${i.author}`} width={100} height={100} />

            <h1>{i.id}</h1>

          </div>


        ))}

      </InfiniteScroll>
    </div>
  )
}

export default App

## DicEnc
1. read_data(_DIC_SIZE_32K * 2)
2. search(min = (_pos-_DIC_SIZE_32K) < 0 ? 0 : (_pos-_DIC_SIZE_32K)) (len<_MAX_MATCH_256 && _pos+len<_read_size)
3. output_buff
4. next_pos

### next_pos
1. _pos += 1
2. if ( _pos >= _DIC_SIZE_32K*2-_MAX_MATCH_256 ) goto 3
3. _dic[i] = _dic[ _DIC_SIZE_32K+i ]
4. _link_list[i] = _link_list[ _DIC_SIZE_32K + i ] - _DIC_SIZE_32K ;
5. _link_start[i] -= _DIC_SIZE_32K
6. _read_size -= _DIC_SIZE_32K
7. read_data

## DicDec
1. if ( code < MATCH_LEN_MIN_257 ) -> output_stream.put( code ) ; _dic[ _pos++ ] = code
2. else len  = code - MATCH_LEN_MIN_257 ; dist = input_stream.dist_get() + 1 ; loop len : output_stream.put( c );_dic[ _pos++ ] = c;

## BBlkOut
literal_put,match_len_put trigger flush
### flush
1. freq_literal.count_up( EOB_256 ) ;  
for ( int i=0, max=_buff.size() ; i<max ; ++i )
2. if ( code <= EOB_256 ) freq_literal.count_up( code ) ;
3. else ml = matlen_cnv.length_to_code( code - MATCH_LEN_MIN_257 ) ;freq_literal.count_up( ml._code ) ;  
ds = dist_cnv.distance_to_code( dist ) ; freq_distance.count_up( ds._code ) ;  
Huffman::HuffEnc  literal_enc( freq_literal, 16 ) ;  
Huffman::HuffEnc  distance_enc( freq_distance, 16 ) ;  
4. ブロックヘッダを出力 ビット長テーブルを出力
5. _buff 出力  if ( code <= EOB_256 ) literal_enc.encode( _bit_strm_o, code ) ;  
else ml = matlen_cnv.length_to_code( code - MATCH_LEN_MIN_257 ) ;literal_enc.encode( _bit_strm_o, ml._code ) ;_bit_strm_o.n_bit_put( ml._bit_pattern, ml._bit_length ) ;  
ds = dist_cnv.distance_to_code( dist ) ;distance_enc.encode( _bit_strm_o, ds._code ) ;_bit_strm_o.n_bit_put( ds._bit_pattern, ds._bit_length ) ;
6. literal_enc.encode( _bit_strm_o, EOB_256 ) ;

## BBlkIn
### dist_get()
1. code = _distance_dec->decode( _bit_strm_i ) 
2. ds = _dist_cnv.code_to_distance( code )
3. dist = ds._distance_base + _bit_strm_i.n_bit_get( ds._bit_length )
### get()
1. if ( _block_mode == MODE_UN_COMPRESS_x00 ) _un_compress_size -= 1 return _bit_strm_i.get()
2. else literal = _literal_dec->decode( _bit_strm_i )  if ( literal <= LITERAL_MAX_255 ) return literal ;   
else ln = _matlen_cnv.code_to_length( literal );literal = ln._length_base + _bit_strm_i.n_bit_get( ln._bit_length ); return  literal + MATCH_LEN_MIN_257;
3. if ( literal == EOB_256 ) block_header_init() ;
### block_header_init()
1. _eof_flag   = _bit_strm_i.n_bit_get( 1 );_block_mode = _bit_strm_i.n_bit_get( 2 );
2. if ( _block_mode == MODE_DYNAMIC_HUFFMAN_x02 ) lit_len_size = _bit_strm_i.n_bit_get( 5 ) + MATCH_LEN_MIN_257;dis_len_size = _bit_strm_i.n_bit_get( 5 ) + 1 ;  
_bitlen_dec = new BitLenHuffman::BLenDec( _bit_strm_i );_literal_dec;_distance_dec;
3. if ( _block_mode == MODE_STATIC_HUFFMAN_x01 ) 
static version _literal_dec;_distance_dec;
4. if ( _block_mode == MODE_UN_COMPRESS_x00 )  
ln1 = _bit_strm_i.get() ;ln2 = _bit_strm_i.get() ;_un_compress_size = ln2*256 + ln1 ;


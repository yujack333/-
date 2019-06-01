# faster-rcnn匹配方法：

匹配方法是：

1.与GT框最大iou的anchor标定为positive

2.与任意GT框的iou大于0.7的anchor标定为positive,小于0.3的为negative，在之间的为ignore

```python
  def _match_when_rows_are_non_empty():
    """Performs matching when the rows of similarity matrix are non empty.

    Returns:
      matches:  int32 tensor indicating the row each column matches to.
    """
    # Matches for each column
    # 匹配的gt框是哪个
    matches = tf.argmax(similarity_matrix, 0, output_type=tf.int32)

    # Deal with matched and unmatched threshold
    if self._matched_threshold is not None:
      # Get logical indices of ignored and unmatched columns as tf.int64
      # 与gt框的最大值
      matched_vals = tf.reduce_max(similarity_matrix, 0)
      # 小于0.3anchor
      below_unmatched_threshold = tf.greater(self._unmatched_threshold,
                                             matched_vals)
      # 介于0.3-0.7之间的anchor
      between_thresholds = tf.logical_and(
          tf.greater_equal(matched_vals, self._unmatched_threshold),
          tf.greater(self._matched_threshold, matched_vals))
      # faster-rcnn 用的是这个TRUE
      if self._negatives_lower_than_unmatched:
        matches = self._set_values_using_indicator(matches,
                                                   below_unmatched_threshold,
                                                   -1)
        matches = self._set_values_using_indicator(matches,
                                                   between_thresholds,
                                                   -2)
      else:
        matches = self._set_values_using_indicator(matches,
                                                   below_unmatched_threshold,
                                                   -2)
        matches = self._set_values_using_indicator(matches,
                                                   between_thresholds,
                                                   -1)
    # 这个就是匹配的第一条，这里确保了每个gt框都会匹配上anchor框
    if self._force_match_for_each_row:
      similarity_matrix_shape = shape_utils.combined_static_and_dynamic_shape(
          similarity_matrix)
      # 找到最大的anchor框
      force_match_column_ids = tf.argmax(similarity_matrix, 1,
                                         output_type=tf.int32)
      # 利用one-hot编码找到anchor框
      force_match_column_indicators = (
          tf.one_hot(
              force_match_column_ids, depth=similarity_matrix_shape[1]) *
          tf.cast(tf.expand_dims(valid_rows, axis=-1), dtype=tf.float32))
      # 找到匹配上的anchor框对应的gt框
      force_match_row_ids = tf.argmax(force_match_column_indicators, 0,
                                      output_type=tf.int32)
      # 找到匹配上的anchor框
      force_match_column_mask = tf.cast(
          tf.reduce_max(force_match_column_indicators, 0), tf.bool)
      # 替换匹配，注意这里会将之前negative或者ignore的anchor框匹配上gt框。
      final_matches = tf.where(force_match_column_mask,
                               force_match_row_ids, matches)
      #最后的返回结果为shape为[M]的tensor，M为anchor的维度，其中的值：-2表示ignore，-1表示negative，其他值表示匹配上的gt框的index。
      return final_matches
    else:
      return matches
    ```




# @SuppressWarnings

@SuppressWarnings("unchecked")

告诉编译器忽略 unchecked 警告信息,如使用 list ArrayList等未进行参数化产生的警告信息

@SuppressWarnings("serial")

如 果编译器出现这样的警告信息: The serializable class WmailCalendar does not declare a static final serialVersionUID field of type long     使用这个注释将警告信息去掉。

@SuppressWarnings("deprecation")

如果使用了使用@Deprecated注释的方法，编译器将出现警告信息。使用这个注释将警告信息去掉。

@SuppressWarnings("unchecked", "deprecation")

告诉编译器同时忽略unchecked和deprecation的警告信息。

@SuppressWarnings(value={"unchecked", "deprecation"})

等同于@SuppressWarnings("unchecked", "deprecation")
